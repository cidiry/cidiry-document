# AMC 数据下载

要在 Python 中实现 **AMC（Amazon Marketing Cloud，亚马逊营销云）数据的下载**，需基于 **AMC API（Amazon Marketing Cloud API）** 开发（AMC 数据下载不通过之前的 SP-API Reports API，而是有专属接口）。以下是完整的实现方案，包含认证流程、数据请求创建、数据下载与解密，同时覆盖 AMC 数据下载的核心前提和注意事项。

### 一、核心前提：AMC 数据下载的准入与配置

在编写代码前，必须完成 AMC 账户的开通与认证信息获取（亚马逊对 AMC 有准入门槛，需先满足条件）：

1. **开通 AMC 账户**：

* 需具备 **品牌备案（Brand Registry）** 资质，且亚马逊广告投放金额达到一定门槛（通常要求近 12 个月广告花费 ≥ 10 万美元，具体以亚马逊客户经理通知为准）。
* 通过亚马逊广告平台（Amazon Advertising Console）申请开通 AMC，审核通过后会收到 AMC 账户激活邮件。

1. **获取 AMC 认证信息**（关键！无此信息无法调用 API）：

   开通 AMC 后，在 **AMC 控制台 →  Settings → API Credentials** 中获取以下信息：

* `AMC_CLIENT_ID`：AMC 应用的客户端 ID
* `AMC_CLIENT_SECRET`：AMC 应用的客户端密钥
* `AMC_REFRESH_TOKEN`：AMC OAuth 2.0 刷新令牌（需手动生成，有效期 1 年）
* `AMC_ENDPOINT`：AMC API 端点（固定为 `https://advertising-api.amazon.com/amc/v1`，全球统一，无区域差异）

1. **明确 AMC 数据类型与参数**：

   需提前确定要下载的 AMC 数据类型（对应 API 端点），例如：

* 广告归因数据：`/data-requests`（创建数据请求，获取归因、转化等核心数据）
* 受众数据：`/audience-data`（获取 AMC 受众细分数据）
* 数据模型模板：`/data-models`（调用预设的数据模型生成报表）

  同时需明确数据的 **时间范围（startDate/endDate）**、**维度（如 campaignId、adGroupId）**、**指标（如 impressions、clicks、sales）** 等参数（参考 [AMC API 官方文档](https://developer-docs.amazon.com/amazon-advertising/docs/amc-api)）。

### 二、Python 实现 AMC 数据下载（完整代码）

AMC 数据下载为 **异步流程**（因数据量庞大，无法实时返回），需分 3 步：

1. 生成 AMC API 访问令牌（OAuth 2.0 认证）；
2. 创建 AMC 数据请求（提交数据查询任务）；
3. 轮询请求状态，待数据生成后下载并解密。

#### 1. 安装依赖库

需用到 `requests`（HTTP 请求）、`cryptography`（AMC 数据解密，AMC 数据默认 AES 加密）：

```python
pip install requests cryptography
```

#### 2. 完整代码（支持 AMC 数据下载与解密）

```python
import requests
import time
import base64
from cryptography.hazmat.primitives.ciphers import Cipher, algorithms, modes
from cryptography.hazmat.backends import default_backend
from cryptography.hazmat.primitives import padding

# --------------------------
# 1. 配置 AMC 认证与数据参数（替换为你的实际信息）
# --------------------------
# AMC 认证信息（从 AMC 控制台 API Credentials 页面获取）
AMC_CLIENT_ID = "你的AMC_CLIENT_ID"
AMC_CLIENT_SECRET = "你的AMC_CLIENT_SECRET"
AMC_REFRESH_TOKEN = "你的AMC_REFRESH_TOKEN"
# AMC API 固定端点（全球统一，无需修改）
AMC_API_ENDPOINT = "https://advertising-api.amazon.com/amc/v1"

# 要下载的 AMC 数据参数（根据业务需求调整，参考 AMC API 文档）
DATA_REQUEST_PARAMS = {
    "dataModelId": "amc预置数据模型ID",  # 必传：AMC 数据模型ID（预置/自定义，从 AMC 控制台获取）
    "startDate": "2024-08-01",          # 数据起始日期（YYYY-MM-DD，AMC 数据保留周期通常为 180 天）
    "endDate": "2024-08-24",            # 数据结束日期（YYYY-MM-DD）
    "dimensions": ["campaignId", "adGroupId"],  # 数据维度（如广告活动ID、广告组ID，参考 AMC 维度列表）
    "metrics": ["impressions", "clicks", "attributedSales14d"],  # 数据指标（曝光、点击、14天归因销售额）
    "filters": [                        # 可选：数据过滤条件（如筛选特定广告活动）
        {
            "field": "campaignId",
            "operator": "IN",
            "values": ["你的广告活动ID1", "你的广告活动ID2"]
        }
    ]
}

# --------------------------
# 2. 第一步：获取 AMC API Access Token（OAuth 2.0 认证）
# --------------------------
def get_amc_access_token():
    """
    从 AMC LWA 服务器获取 Access Token（有效期 1 小时，过期需用 Refresh Token 重新获取）
    参考：AMC API 官方认证文档 https://developer-docs.amazon.com/amazon-advertising/docs/amc-api-authentication
    """
    auth_url = "https://api.amazon.com/auth/o2/token"
    payload = {
        "grant_type": "refresh_token",
        "refresh_token": AMC_REFRESH_TOKEN,
        "client_id": AMC_CLIENT_ID,
        "client_secret": AMC_CLIENT_SECRET
    }
    try:
        response = requests.post(auth_url, data=payload, timeout=10)
        response.raise_for_status()  # 若请求失败（如401/403），抛出异常
        token_data = response.json()
        return token_data["access_token"]
    except Exception as e:
        raise Exception(f"AMC Access Token 获取失败：{str(e)}，请检查认证信息是否正确")

# --------------------------
# 3. 第二步：创建 AMC 数据请求（异步生成数据）
# --------------------------
def create_amc_data_request(access_token):
    """
    提交 AMC 数据查询请求，返回请求ID（dataRequestId），后续用该ID查询数据状态
    参考：AMC API Create Data Request 文档 https://developer-docs.amazon.com/amazon-advertising/docs/amc-api-operations#create-data-request
    """
    create_url = f"{AMC_API_ENDPOINT}/data-requests"
    headers = {
        "Authorization": f"Bearer {access_token}",
        "Content-Type": "application/json",
        "Accept": "application/json"
    }
    try:
        response = requests.post(
            create_url,
            json=DATA_REQUEST_PARAMS,
            headers=headers,
            timeout=15
        )
        response.raise_for_status()
        request_data = response.json()
        return request_data["dataRequestId"]  # 关键：数据请求ID，用于后续轮询和下载
    except Exception as e:
        error_msg = response.json().get("message", str(e)) if response.text else str(e)
        raise Exception(f"AMC 数据请求创建失败：{error_msg}，请检查数据参数（如dataModelId、日期范围）")

# --------------------------
# 4. 第三步：轮询 AMC 数据请求状态（等待数据生成）
# --------------------------
def poll_amc_data_request(access_token, data_request_id):
    """
    轮询数据请求状态，直到状态变为 SUCCESS（成功）或 FAILED（失败）
    AMC 数据生成时间：视数据量而定，小数据量（如单广告活动）约 1-5 分钟，大数据量（如全账户）可能 10-30 分钟
    参考：AMC API Get Data Request 文档 https://developer-docs.amazon.com/amazon-advertising/docs/amc-api-operations#get-data-request
    """
    poll_url = f"{AMC_API_ENDPOINT}/data-requests/{data_request_id}"
    headers = {
        "Authorization": f"Bearer {access_token}",
        "Accept": "application/json"
    }
    # 轮询配置：最大30次，间隔10分钟（避免频繁调用触发限流）
    max_retries = 30
    retry_interval = 60  # 单位：秒
    for retry in range(max_retries):
        try:
            response = requests.get(poll_url, headers=headers, timeout=10)
            response.raise_for_status()
            request_status = response.json()["status"]
            print(f"AMC 数据请求状态（第{retry+1}次轮询）：{request_status}")

            if request_status == "SUCCESS":
                # 数据生成成功，返回数据下载URL和解密信息
                return {
                    "download_url": response.json()["result"]["downloadUrl"],
                    "encryption_key": response.json()["result"]["encryptionDetails"]["key"],  # base64编码的AES密钥
                    "initialization_vector": response.json()["result"]["encryptionDetails"]["initializationVector"]  # base64编码的IV
                }
            elif request_status == "FAILED":
                # 数据生成失败，获取失败原因
                error_detail = response.json().get("errorDetails", "未知失败原因")
                raise Exception(f"AMC 数据生成失败：{error_detail}")
            else:
                # 状态为 PENDING（待处理）或 IN_PROGRESS（处理中），继续轮询
                time.sleep(retry_interval)
        except Exception as e:
            print(f"轮询 AMC 数据状态失败（第{retry+1}次）：{str(e)}，{retry_interval}秒后重试")
            time.sleep(retry_interval)
    raise Exception(f"超过最大轮询次数（{max_retries}次），AMC 数据仍未生成，请检查数据请求规模或联系亚马逊AMC支持")

# --------------------------
# 5. 第四步：下载并解密 AMC 数据（AMC 数据默认AES-256-GCM加密）
# --------------------------
def decrypt_amc_data(encrypted_content, encryption_key_b64, iv_b64):
    """
    解密 AMC 下载的加密数据（AMC 采用 AES-256-GCM 加密，需用返回的密钥和IV解密）
    参考：AMC API 数据解密文档 https://developer-docs.amazon.com/amazon-advertising/docs/amc-data-encryption
    """
    try:
        # 解码 base64 格式的密钥和IV
        key = base64.b64decode(encryption_key_b64)
        iv = base64.b64decode(iv_b64)

        # 初始化 AES-GCM 解密器（AMC 固定加密模式）
        cipher = Cipher(algorithms.AES(key), modes.GCM(iv), backend=default_backend())
        decryptor = cipher.decryptor()

        # 解密数据（AMC 加密数据无额外padding，直接解密）
        decrypted_content = decryptor.update(encrypted_content) + decryptor.finalize()
        return decrypted_content.decode("utf-8")  # 解密后为 CSV/TSV 文本格式
    except Exception as e:
        raise Exception(f"AMC 数据解密失败：{str(e)}，请检查密钥和IV是否正确")

def download_and_save_amc_data(download_url, encryption_key_b64, iv_b64, save_path):
    """下载 AMC 加密数据并解密保存到本地文件"""
    try:
        # 下载加密数据（AMC 数据为二进制流）
        response = requests.get(download_url, timeout=30)
        response.raise_for_status()
        encrypted_content = response.content
        print(f"AMC 加密数据下载完成，大小：{len(encrypted_content)/1024:.2f} KB")

        # 解密数据
        decrypted_data = decrypt_amc_data(encrypted_content, encryption_key_b64, iv_b64)

        # 保存解密后的数据（AMC 数据默认 CSV 格式，可根据实际调整后缀）
        with open(save_path, "w", encoding="utf-8") as f:
            f.write(decrypted_data)
        print(f"AMC 数据已解密并保存到：{save_path}")
        return save_path
    except Exception as e:
        raise Exception(f"AMC 数据下载/保存失败：{str(e)}")

# --------------------------
# 主流程：整合所有步骤
# --------------------------
if __name__ == "__main__":
    try:
        # 1. 获取 Access Token
        print("第一步：获取 AMC Access Token...")
        amc_access_token = get_amc_access_token()
        print("AMC Access Token 获取成功")

        # 2. 创建 AMC 数据请求
        print("\n第二步：创建 AMC 数据请求...")
        amc_data_request_id = create_amc_data_request(amc_access_token)
        print(f"AMC 数据请求创建成功，dataRequestId：{amc_data_request_id}")

        # 3. 轮询数据请求状态
        print("\n第三步：轮询 AMC 数据生成状态...")
        data_download_info = poll_amc_data_request(amc_access_token, amc_data_request_id)

        # 4. 下载并保存数据
        print("\n第四步：下载并解密 AMC 数据...")
        save_file_path = f"amc_data_{amc_data_request_id}.csv"  # 保存路径（CSV格式）
        download_and_save_amc_data(
            download_url=data_download_info["download_url"],
            encryption_key_b64=data_download_info["encryption_key"],
            iv_b64=data_download_info["initialization_vector"],
            save_path=save_file_path
        )
        print("\nAMC 数据下载流程全部完成！")
    except Exception as e:
        print(f"\nAMC 数据下载失败：{str(e)}")
```

### 三、关键说明（必须关注，避免踩坑）

#### 1. AMC 数据模型 ID（dataModelId）的获取

`dataModelId` 是 AMC 数据请求的核心参数，需从 **AMC 控制台 → Data Models** 页面获取：

* 预置模型：亚马逊提供的通用模型（如 “Advertising Attribution” 广告归因模型、“Audience Performance” 受众表现模型），可直接复制模型 ID；
* 自定义模型：若你在 AMC 中创建了自定义数据模型（如自定义维度 / 指标组合），需用自定义模型的 ID。

#### 2. AMC 数据的特殊限制（官方规则）

* **数据保留周期**：AMC 仅保留近 180 天的广告数据，`startDate` 不能早于当前日期前 180 天；
* **调用频率限制**：AMC API 限制为 **5 QPS（每秒请求数）**，轮询时需保持足够间隔（代码中已设 60 秒，避免触发 429 限流）；
* **数据规模限制**：单次请求的数据范围不宜过大（如全账户 180 天数据），可能导致生成时间过长或失败，建议按 “周 / 月” 拆分请求。

#### 3. 常见错误与解决方案（参考 AMC 官方错误文档）

| 错误码 | 错误原因                                      | 解决方案                                                                                          |
| ------ | --------------------------------------------- | ------------------------------------------------------------------------------------------------- |
| 401    | Access Token 过期 / 无效、认证信息错误        | 重新调用 `get_amc_access_token()` 生成令牌，核对 AMC 认证信息                                   |
| 403    | 无 AMC 数据访问权限、数据模型 ID 无权限       | 联系亚马逊广告客户经理开通 AMC 权限，确认 `dataModelId` 属于当前账户                            |
| 400    | 日期格式错误、维度 / 指标不存在、筛选条件无效 | 核对 `startDate`/`endDate` 为 YYYY-MM-DD，参考 AMC 官方 “Dimensions & Metrics” 列表检查参数 |
| 429    | API 调用频率超限                              | 增加轮询间隔（如从 60 秒改为 120 秒）                                                             |
| 500    | 亚马逊 AMC 服务器临时故障                     | 等待 5-10 分钟后重新发起请求，保留 `dataRequestId` 以便后续查询                                 |

### 四、后续优化建议

1. **Token 缓存**：将获取的 Access Token 缓存到本地文件（如 `amc_token.json`），过期前重新获取，避免每次请求都调用认证接口；
2. **异常重试**：在 `get_amc_access_token()`、`download_and_save_amc_data()` 等函数中添加重试逻辑（如 `tenacity` 库），提升稳定性；
3. **批量请求**：若需下载多维度 / 多时间范围的 AMC 数据，可封装批量请求函数，并用多线程（需控制并发数，避免限流）提高效率；
4. **数据解析**：解密后的 AMC 数据为 CSV 格式，可使用 `pandas` 库解析（如 `pd.read_csv(save_file_path)`），方便后续数据分析（如计算 ROI、归因效果）。

如需进一步适配特定 AMC 数据类型（如受众数据、自定义模型数据），可参考 [AMC API 官方操作文档](https://developer-docs.amazon.com/amazon-advertising/docs/amc-api-operations) 调整 `DATA_REQUEST_PARAMS` 中的参数（如 `dataModelId`、`dimensions`、`metrics`）。

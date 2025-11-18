# 亚马逊营销云（AMC）报告下载教程

如果你有一个亚马逊营销云（AMC）账号，想要下载报告，可以参考以下步骤：

### 1. 确认账号权限

确保你的 AMC 账号具有下载报告的权限。你可以联系亚马逊广告团队或相关管理员，确认账号是否被授予了相应的数据访问权限。

### 2. 获取认证信息

在 AMC 控制台的 “Settings”→“API Credentials” 中，获取以下信息：

* `AMC_CLIENT_ID`：AMC 应用的客户端 ID。
* `AMC_CLIENT_SECRET`：AMC 应用的客户端密钥。
* `AMC_REFRESH_TOKEN`：AMC OAuth 2.0 刷新令牌。

### 3. 确定报告类型和参数

根据你的需求，确定要下载的 AMC 报告类型，并准备好相关参数：

* **报告类型**：例如，广告归因数据对应 `/data - requests`端点，受众数据对应 `/audience - data`端点等。
* **时间范围**：明确数据的起始日期（`startDate`）和结束日期（`endDate`），日期格式需为 YYYY - MM - DD。
* **维度和指标**：选择你想要包含在报告中的维度（如 `campaignId`、`adGroupId`）和指标（如 `impressions`、`clicks`、`attributedSales14d`）。
* **筛选条件（可选）**：根据需要设置筛选条件，如筛选特定的广告活动等。

### 4. 调用 AMC API 创建数据请求

你可以使用 Python 中的 `requests`库来发送 HTTP 请求与 AMC API 进行交互。以下是一个简单的示例代码，用于创建数据请求：

```python
import requests

# AMC API固定端点
AMC_API_ENDPOINT = "https://advertising-api.amazon.com/amc/v1"
# 从AMC控制台获取的认证信息
AMC_CLIENT_ID = "你的AMC_CLIENT_ID"
AMC_CLIENT_SECRET = "你的AMC_CLIENT_SECRET"
AMC_REFRESH_TOKEN = "你的AMC_REFRESH_TOKEN"

# 获取AMC API访问令牌
def get_amc_access_token():
    auth_url = "https://api.amazon.com/auth/o2/token"
    payload = {
        "grant_type": "refresh_token",
        "refresh_token": AMC_REFRESH_TOKEN,
        "client_id": AMC_CLIENT_ID,
        "client_secret": AMC_CLIENT_SECRET
    }
    response = requests.post(auth_url, data=payload)
    response.raise_for_status()
    return response.json()["access_token"]

access_token = get_amc_access_token()

# 创建AMC数据请求
def create_amc_data_request(access_token):
    create_url = f"{AMC_API_ENDPOINT}/data - requests"
    headers = {
        "Authorization": f"Bearer {access_token}",
        "Content - Type": "application/json",
        "Accept": "application/json"
    }
    # 替换为实际的报告参数
    data_request_params = {
        "dataModelId": "amc预置数据模型ID",
        "startDate": "2024 - 08 - 01",
        "endDate": "2024 - 08 - 24",
        "dimensions": ["campaignId", "adGroupId"],
        "metrics": ["impressions", "clicks", "attributedSales14d"]
    }
    response = requests.post(create_url, json=data_request_params, headers=headers)
    response.raise_for_status()
    return response.json()["dataRequestId"]

data_request_id = create_amc_data_request(access_token)
print(f"AMC数据请求创建成功，dataRequestId：{data_request_id}")
```

### 5. 轮询数据请求状态

数据请求是异步处理的，你需要轮询请求状态，直到状态变为 `SUCCESS`，表示数据已生成完毕。以下是一个轮询状态的示例代码：

```
def poll_amc_data_request(access_token, data_request_id):
    poll_url = f"{AMC_API_ENDPOINT}/data - requests/{data_request_id}"
    headers = {
        "Authorization": f"Bearer {access_token}",
        "Accept": "application/json"
    }
    max_retries = 30
    retry_interval = 60
    for _ in range(max_retries):
        response = requests.get(poll_url, auth=aws_auth, headers=headers)
        response.raise_for_status()
        request_status = response.json()["status"]
        if request_status == "SUCCESS":
            return response.json()
        elif request_status == "FAILED":
            error_detail = response.json().get("errorDetails", "未知失败原因")
            raise Exception(f"AMC数据生成失败：{error_detail}")
        else:
            time.sleep(retry_interval)
    raise Exception(f"超过最大轮询次数（{max_retries}次），AMC数据仍未生成")

data_download_info = poll_amc_data_request(access_token, data_request_id)
```

### 6. 下载并解密报告数据

当数据请求状态变为 `SUCCESS`后，你可以从响应中获取下载链接和加密信息，下载报告并进行解密。以下是下载并解密报告数据的示例代码：

```python
def decrypt_amc_data(encrypted_content, encryption_key_b64, iv_b64):
    from cryptography.hazmat.primitives.ciphers import Cipher, algorithms, modes
    from cryptography.hazmat.backends import default_backend
    from cryptography.hazmat.primitives import padding
    import base64
    key = base64.b64decode(encryption_key_b64)
    iv = base64.b64decode(iv_b64)
    cipher = Cipher(algorithms.AES(key), modes.GCM(iv), backend=default_backend())
    decryptor = cipher.decryptor()
    decrypted_content = decryptor.update(encrypted_content) + decryptor.finalize()
    return decrypted_content.decode("utf - 8")

def download_and_save_amc_data(download_url, encryption_key_b64, iv_b64, save_path):
    response = requests.get(download_url)
    response.raise_for_status()
    encrypted_content = response.content
    decrypted_data = decrypt_amc_data(encrypted_content, encryption_key_b64, iv_b64)
    with open(save_path, "w", encoding="utf - 8") as f:
        f.write(decrypted_data)
    print(f"AMC数据已解密并保存到：{save_path}")

download_and_save_amc_data(
    download_url=data_download_info["download_url"],
    encryption_key_b64=data_download_info["encryptionDetails"]["key"],
    iv_b64=data_download_info["encryptionDetails"]["initializationVector"],
    save_path="amc_report.csv"
)
```

在整个过程中，你需要仔细参考 AMC API 的官方文档，确保请求参数的正确性和权限的有效性。如果在操作过程中遇到问题，可以联系亚马逊广告支持团队寻求帮助。

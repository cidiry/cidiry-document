以下是 **Amazon Selling Partner API（SP-API）权限获取的完整流程**，从注册到最终调用API的详细步骤：

### **一、前期准备**

1. **拥有 Amazon 卖家账户**
    - 需要有效的 **Seller Central** 或 **Vendor Central** 账号，并完成品牌注册（Amazon Brand Registry）。
2. **注册 Amazon 开发者账号**
    - 访问 [Amazon Developer Central](https://developer.amazon.com/) ，用卖家账号登录并完成开发者注册。

### **二、创建 IAM 角色（AWS 身份管理）**

SP-API 要求通过 AWS IAM 角色进行身份验证。  
**步骤：**

1. 登录 [AWS Management Console](https://aws.amazon.com/)。
2. 进入 **IAM → Roles → Create Role**：
    - **可信实体类型**：选择 **“AWS 账户”** 。
    - **账户 ID**：输入 **Amazon 的卖家账户 ID**（可在 Seller Central 的“设置 → 账户信息”底部找到）。
    - **附加权限策略**：选择 **`AWSMarketplaceFullAccess`** （或自定义权限）。
3. 记录生成的 **IAM Role ARN**（后续步骤需要）。

### **三、在开发者中心创建应用**

1. **创建新应用**
    - 登录 [Amazon Developer Central](https://developer.amazon.com/) → **APPS & SERVICES → Develop Apps** → **Create App**。
    - 填写应用名称和描述（例如 `Brand Analytics Downloader`）。
2. **配置 API 权限**
    - 在应用详情页，进入 **API Permissions**：
        - 勾选 **“Selling Partner API”** → 选择需要的数据权限（例如 `Brand Analytics`、`Reports`）。
        - 点击 **Generate LWA Credentials**（生成客户端密钥）。
3. **获取关键凭证**
    - 记录以下信息：
        - **Client ID**（客户端ID）
        - **Client Secret**（客户端密钥）
        - **IAM Role ARN**（上一步 AWS 生成的ARN）

### **四、在 Seller Central 授权应用**

1. **导航到开发者权限页面**
    - 登录 Seller Central → **设置 → 用户权限 → 开发人员权限**。
2. **授权应用**
    - 输入 **Client ID** 和 **IAM Role ARN** → 点击 **下一步** → 确认权限范围。
3. **获取 Seller ID 和 Marketplace ID**
    - **Seller ID**：在 Seller Central 的“账户信息”页面底部获取。
    - **Marketplace ID**：根据运营站点选择（例如北美站为 `ATVPDKIKX0DER`）。

### **五、获取 OAuth 2.0 访问令牌**

SP-API 使用 **OAuth 2.0 授权码流程** 获取 `Access Token`。  
**步骤：**

1. **生成授权 URL**  
    替换以下参数生成 URL：
    
    ```text
    https://sellercentral.amazon.com/apps/authorize/consent?
    application_id={Client_ID}
    &state={随机字符串}
    &version=beta
    ```
    
2. **用户授权**
    - 访问生成的 URL → 使用卖家账号登录 → 点击 **确认**，系统返回 **授权码（Authorization Code）** 。
3. **换取访问令牌**  
    使用 `POST` 请求获取 `access_token` 和 `refresh_token`：
    
    ```python
    import requests
    
    url = "https://api.amazon.com/auth/o2/token"
    data = {
        "grant_type": "authorization_code",
        "code": "你的授权码",
        "client_id": "你的Client ID",
        "client_secret": "你的Client Secret"
    }
    response = requests.post(url, data=data)
    tokens = response.json()
    access_token = tokens['access_token']
    refresh_token = tokens['refresh_token']  # 长期有效，需安全存储！
    ```


### **六、调用 SP-API 接口**

以获取品牌分析报告为例：

1. **生成报告请求**
    
    ```python
    headers = {
        "x-amz-access-token": access_token,
        "Content-Type": "application/json"
    }
    params = {
        "reportType": "GET_BRAND_ANALYTICS_SEARCH_TERMS_REPORT",
        "dataStartTime": "2023-01-01T00:00:00Z",
        "dataEndTime": "2023-01-31T23:59:59Z"
    }
    response = requests.get(
        "https://sellingpartnerapi-eu.amazon.com/reports/2021-06-30/reports",
        headers=headers,
        params=params
    )
    report_id = response.json()['reportId']
    ```
    
2. **下载报告**  
    报告生成后，通过 `getReport` 接口下载：
    
    ```python
    download_url = f"https://sellingpartnerapi-eu.amazon.com/reports/2021-06-30/reports/{report_id}"
    response = requests.get(download_url, headers=headers)
    with open("brand_analytics_report.csv", "wb") as f:
        f.write(response.content)
    ```
    

### **七、权限维护与更新**

1. **刷新访问令牌**  
    `access_token` 有效期为1小时，需定期用 `refresh_token` 刷新：
    
    ```python
    data = {
        "grant_type": "refresh_token",
        "refresh_token": "你的Refresh Token",
        "client_id": "Client ID",
        "client_secret": "Client Secret"
    }
    response = requests.post("https://api.amazon.com/auth/o2/token", data=data)
    ```
    
2. **监控 API 限制**
    - SP-API 的速率限制为 **每秒 2 次请求**，超出会返回 `429` 错误，需添加重试机制。

### **常见问题与解决**

1. **权限被拒绝（403错误）**
    - 检查 IAM Role ARN 是否正确绑定。
    - 确保 Seller Central 中已授权应用。
2. **授权码过期**
    - 授权码有效期为5分钟，需快速换取 `access_token`。
3. **跨区域调用**
    - API 端点需与卖家账户区域匹配（例如北美站用 `https://sellingpartnerapi-na.amazon.com`）。

### **最佳实践**

- **安全存储凭证**：使用 AWS Secrets Manager 或 HashiCorp Vault 管理密钥。
- **自动化令牌刷新**：通过定时任务定期更新 `access_token`。
- **错误重试机制**：使用指数退避策略处理速率限制错误。
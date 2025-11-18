
## 项目目标
开发两个基于 FastAPI 的 API 接口，用于管理店铺销售目标和库存数据，需包含完整的参数校验、安全认证和错误处理机制。


### 技术栈要求



*   **后端框架**：FastAPI Uvicorn


*   **编程语言**：Python


*   **安全认证**：JWT Token + OAuth2


*   **数据校验**：Pydantic V2


*   **数据库**：SQLite（开发环境）/ MYSQL（生产环境）

### 接口 1：销售目标管理



*   **路径**：`POST /api/v1/sales/target`

*   **功能**：创建或更新店铺的月度 / 周度销售目标


#### 请求参数（JSON 格式，所有字段必填）



```TypeScript
{
"seller_id": str,        # 店铺ID，长度8-20位字母数字组合
"parent_asin": str,      # 父ASIN，长度10-15位字母数字组合
"market_place_id": str,  # 市场ID，格式如 "US"、"EU"、"JP"
"period": str,           # 周期，格式：YYYY-MM（月）或 YYYY-WW（周）
"sales_target": float    # 销售目标金额，正数，保留2位小数
}
```

#### 响应结果



```TypeScript
{
"code": 200,
"message": "Success",
"data": {
    "id": int,               # 记录ID
    "seller_id": str,
    "parent_asin": str,
    "market_place_id": str,
    "period": str,
    "sales_target": float,
    "create_time": str,      # 创建时间，ISO格式
    "update_time": str       # 更新时间，ISO格式
}
}
```

### 接口 2：库存管理



*   **路径**：`POST /api/v1/inventory`

*   **功能**：创建或更新商品库存信息


#### 请求参数（JSON 格式，所有字段必填）&#xA;



```TypeScript
{
"seller_id": str,        # 店铺ID，长度8-20位字母数字组合
"parent_asin": str,      # 父ASIN，长度10-15位字母数字组合
"child_asin": str,       # 子ASIN，长度10-15位字母数字组合
"seller_sku": str,       # 卖家SKU，长度10-30位字母数字组合
"market_place_id": str,  # 市场ID，格式如 "US"、"EU"、"JP"
"inventory": int         # 库存量，非负整数
}
```

#### 响应结果



```TypeScript
{
"code": 200,
"message": "Success",
"data": {
    "id": int,               # 记录ID
    "seller_id": str,
    "parent_asin": str,
    "child_asin": str,
    "seller_sku": str,
    "market_place_id": str,
    "inventory": int,
    "create_time": str,      # 创建时间，ISO格式
    "update_time": str       # 更新时间，ISO格式
}
}
```

### 安全要求



1.  **认证机制**：


*   使用 OAuth2 密码模式（Password Flow）获取 JWT Token


*   Token 有效期：访问 Token（access_token）2 小时，刷新 Token（refresh_token）7 天


*   接口需验证`Authorization: Bearer <token>`头


1.  **权限控制**：


*   所有接口需用户登录后访问


*   实现 RBAC 角色权限控制（预留`admin`/`seller`角色字段）


1.  **数据安全**：


*   敏感数据（如 Token）需加密传输（强制 HTTPS）


*   数据库连接字符串需从环境变量读取


*   接口限流：单个 IP 60 请求 / 分钟


### 数据模型&#xA;



1.  **销售目标表**：




```TypeScript
class SalesTarget(BaseModel):


id: int


seller_id: str


parent_asin: str


market_place_id: str


period: str  # 格式校验：YYYY-MM 或 YYYY-WW


sales_target: float


create_time: datetime


update_time: datetime
```



1.  **库存表**：




```TypeScript
class Inventory(BaseModel):


id: int


seller_id: str


parent_asin: str


child_asin: str


seller_sku: str


market_place_id: str


inventory: int


create_time: datetime


update_time: datetime
```

### 其他要求



1.  **错误处理**：


*   参数校验失败返回 422 状态码及错误详情


*   未授权访问返回 401 状态码


*   资源不存在返回 404 状态码


*   系统错误返回 500 状态码


1.  **日志记录**：


*   记录请求 IP、时间、接口路径、响应状态码


*   敏感信息（如 Token）需脱敏处理


1.  **代码结构**：




```
project/


├── main.py               # 主应用入口


├── routers/              # 路由模块


│   ├── sales.py          # 销售目标接口


│   └── inventory.py      # 库存接口


├── models/               # 数据模型


│   ├── sales.py


│   └── inventory.py


├── schemas/              # 请求/响应模式


│   ├── sales.py


│   └── inventory.py


├── utils/                # 工具函数


│   ├── auth.py           # 认证相关


│   └── database.py       # 数据库连接


├── .env                  # 环境变量配置


└── requirements.txt      # 依赖清单
```



1.  **环境变量配置**：




```
DATABASE_URL=sqlite:///./test.db  # 开发环境


SECRET_KEY=your-secret-key        # JWT密钥


ALGORITHM=HS256                   # 加密算法


ACCESS_TOKEN_EXPIRE_MINUTES=120   # 访问Token有效期（分钟）


REFRESH_TOKEN_EXPIRE_DAYS=7       # 刷新Token有效期（天）
```

### 交付要求



1.  完整的 FastAPI 项目代码（按上述结构组织）


2.  数据库初始化脚本（SQLAlchemy 模型或迁移文件）


3.  接口文档（OpenAPI/Swagger 格式，自动生成）


4.  测试用例（使用`pytest`框架，覆盖正常和异常场景）


5.  部署说明（Dockerfile 或启动命令）


6.  安全配置指南（HTTPS 证书配置、环境变量设置）

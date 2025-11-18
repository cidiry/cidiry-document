# Amazon Best-Sellers 类目抓取需求文档

## 一、项目背景

为实现对 Amazon 平台 Best-Sellers 板块类目体系的结构化采集与分析，支撑后续市场调研、商品类目布局等业务需求，需通过爬虫技术抓取 Amazon Best-Sellers 页面（https://www.amazon.com/gp/bestsellers）的全量类目及子类目信息，并结构化存储至数据库，为业务决策提供数据基础。

## 二、项目目标

1. 使用 Feapder 框架抓取 Amazon Best-Sellers 页面的**所有类目及子类目**（含一级类目、二级类目、三级类目等完整层级结构）；
2. 提取并存储指定字段信息，确保数据完整、准确、层级关联正确；
3. 实现数据自动写入数据库，支持后续查询与分析。

## 三、抓取范围与对象

1. **目标页面**：Amazon Best-Sellers 首页及所有子类目页面（以目标链接为起点，递归抓取所有层级类目页面）；
2. **类目范围**：页面中展示的所有类目体系，包括但不限于：
   * 一级类目（如参考文档中的 “Sports & Outdoors”“Kitchen & Dining” 等）；
   * 二级及以下子类目（如 “Sports & Outdoors” 下的细分品类，具体以页面实际展示为准）。

## 四、技术选型

| 技术 / 工具 | 说明                                                                    |
| ----------- | ----------------------------------------------------------------------- |
| 爬虫框架    | Feapder（轻量级 Python 爬虫框架，支持分布式、自动重试、数据存储等功能） |
| 解析工具    | lxml/BeautifulSoup（用于解析 HTML 页面，提取类目信息）                  |
| 数据库      | MySQL（关系型数据库，适合存储结构化类目数据，支持层级关联查询）         |
| 辅助工具    | 随机 User-Agent 库（模拟浏览器请求）、time 库（控制请求频率）           |
| 代理ip池    | 用户提供代理IP获取链接 验证的账号和密码                                 |

## 五、数据字段说明

| 字段名         | 定义                       | 数据类型     | 备注                                                                                                                                                                                    |
| -------------- | -------------------------- | ------------ | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| marketplace_id | 站点标识                   | varchar      | 区分不同 Amazon 站点（如：ATVPDKIKX0DER=[amazon.com](https://amazon.com/)，A1F83G8C2ARO7P=[amazon.co.uk](https://amazon.co.uk/)等，本项目默认 ATVPDKIKX0DER）                                 |
| category\_id   | 类目 ID                    | varchar(255) | 优先提取 Amazon 内部类目 ID（如链接中 “https://www.amazon.com/Best-Sellers-Clothing-Shoes-Jewelry-Boys-Fashion/zgbs/fashion/7147443011” 的7147443011）；若一级类目无 ID，用类目名代替 |
| category\_name | 类目名称                   | varchar(255) | 如 “Sports & Outdoors”“Kitchen & Dining”                                                                                                                                            |
| level          | 层级                       | int          | 一级类目 = 1，二级类目 = 2，以此类推                                                                                                                                                    |
| parent\_id     | 父类 ID                    | varchar(255) | 对应父类目 “category\_id”；一级类目父类 ID 为 null                                                                                                                                    |
| create\_time   | 创建时间                   | datetime     | 数据存入数据库的时间（格式：YYYY-MM-DD HH:MM:SS）                                                                                                                                       |
| url            | 类目对应 Best-Sellers 链接 | varchar(512) | 该类目下 Best-Sellers 页面的完整 URL 例如：https://www.amazon.com/gp/bestsellers/appliances/3741261  可以拼接而成                                                                      |

## 六、数据库设计

### 表名：`amazon_bestseller_categories`

| 字段名         | 数据类型     | 约束                         | 说明                       |
| -------------- | ------------ | ---------------------------- | -------------------------- |
| id             | int          | PRIMARY KEY, AUTO\_INCREMENT | 自增主键，唯一标识一条记录 |
| marketplace_id | varchar(255) | NOT NULL                     | 站点 ID                    |
| category\_id   | varchar(255) | NOT NULL                     | 类目 ID（或类目名）        |
| category\_name | varchar(255) | NOT NULL                     | 类目名称                   |
| level          | int          | NOT NULL                     | 类目层级                   |
| parent\_id     | varchar(255) |                              | 父类 ID（一级类目为 null） |
| create\_time   | datetime     | NOT NULL                     | 创建时间                   |
| url            | varchar(512) | NOT NULL                     | 类目 Best-Sellers 链接     |

## 七、抓取流程设计（基于 Feapder）

### 1. 初始化配置

* 定义 Feapder 爬虫类（继承 `Spider`或 `AirSpider`），设置爬虫名称（如 “amazon\_bestseller\_categories\_spider”）；
* 配置请求头（User-Agent 随机切换，模拟 Chrome、Safari 等浏览器）；
* 设置请求间隔（如 1-3 秒 / 次），避免触发反爬；
* 配置数据库连接（MySQL 地址、端口、用户名、密码、数据库名）。

### 2. 核心抓取步骤

#### 步骤 1：抓取一级类目

* **入口请求**：请求目标链接（https://www.amazon.com/gp/bestsellers），获取首页 HTML；
* **解析逻辑**：提取页面中所有一级类目（如 “Sports & Outdoors”“Kitchen & Dining” 等）的信息：
  * `category_name`：直接提取类目名称文本；
  * `bestseller_url`：提取类目对应的 Best-Sellers 页面链接（如 “Sports & Outdoors” 的链接）；
  * `category_id`：从链接中提取 “https://www.amazon.com/Best-Sellers-Clothing-Shoes-Jewelry-Boys-Fashion/zgbs/fashion/7147443011” 的7147443011作为 ID；若无则用 链接中的fashion代替；
  * `level`：固定为 1；
  * `parent_id`：固定为 null；
  * `create_time`：当前系统时间。
* **数据存储**：将解析的一级类目数据写入数据库。

#### 步骤 2：递归抓取子类目

* **触发条件**：对每个一级类目链接发起请求，判断页面是否存在二级类目；
* **解析逻辑**：针对二级类目页面，提取信息：
  * `category_name`：二级类目名称；
  * `bestseller_url`：二级类目对应的 Best-Sellers 链接；
  * `category_id`：从链接提取 ID，无则用名称；
  * `level`：固定为 2；
  * `parent_id`：对应一级类目的 `category_id`；
  * `create_time`：当前系统时间。
* **递归处理**：若二级类目页面存在三级类目，重复上述逻辑（`level=3`，`parent_id`为二级类目 ID），直至无更深层级类目。

### 3. 数据存储逻辑

* 每解析一个类目，立即通过 Feapder 的 `db_client`将数据插入 `amazon_bestseller_categories`表；

## 八、异常处理

1. **请求失败**：
   * 当页面请求返回非 200 状态码时，触发重试机制（最多重试 5 次），重试时更换IP；
   * 重试失败后，记录错误日志（含 URL、错误码、时间），跳过该链接。
2. **解析失败**：
   * 若页面结构异常导致类目信息无法提取，记录日志（含 URL、异常信息），跳过该页面。
3. **数据库异常**：
   * 连接失败时，重试连接（最多 5 次）；
   * 插入失败时，记录日志（含数据、错误信息），后续手动处理。

## 九、反爬策略

1. **请求频率控制**：设置随机间隔（1-3 秒），避免高频请求；
2. **User-Agent 伪装**：使用 `fake_useragent`库随机生成浏览器标识；
3. **Cookie 处理**：启用 Feapder 的 `cookie_pool`，自动管理 Cookie（用户提供cookie）；
4. **代理 IP**：若触发 Amazon 反爬（如 403、503），接入代理 IP 池（需要用户提供提取IP的链接以及验证的账号密码）。

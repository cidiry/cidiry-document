## 调研  

一、站点
amazon有17个站点，每个站点的各页面结构可能不一样，并且同站点不同asin的详情页结构也可能不一样

二、cookie

|      | 不带邮编                            | 带邮编             |
| ---- | ------------------------------- | --------------- |
| 搜索   | 需要session-id和ubid-main          | 获取到前面俩后需要更改邮编地址 |
| 商品详情 | session-id: 132-1849752-<随机6位数> | -               |
| 卖家详情 | session-id: 132-1849752-<随机6位数> | -               |
| 榜单   | session-id: 132-1849752-<随机6位数> | -               |
三、代理
经测试，国内代理也可以，失败率较高。


## 数据库设计  

### 一、搜索
#### -> 任务表字段

| id        |        |
| --------- | ------ |
| market_id | 站点id   |
| post_code | 邮编     |
| keyword   | 搜索关键词  |
| turn_page | 翻页数    |
| add_date  | 任务添加时间 |
| state     | 任务状态   |
#### -> 数据表字段

| id                   |           |
| -------------------- | --------- |
| market_id            | 站点id      |
| post_code            | 邮编        |
| data_asin            | asin      |
| keyword              | 搜索关键词     |
| page                 | 第几页       |
| search_rank          | 搜索排名      |
| title                | 商品标题      |
| link                 | 商品链接      |
| main_img_url         | 主图链接      |
| reviews_stars        | 评分        |
| reviews_ratings      | 评论数量      |
| coupon_info          | 优惠信息      |
| scribing_price       | 划线价       |
| selling_price        | 售价        |
| is_sponsored         | 是否是广告     |
| sales_volume         | 过去几个月购买信息 |
| delivery_information | 物流信息      |
| small_business       | 是否是小公司    |
| brand                | 品牌        |
| number_of_sub_asin   | 子asin数量   |
| crawl_date           | 抓取时间      |
### 二、商品详情

#### -> 任务表字段

| id        |        |
| --------- | ------ |
| market_id | 站点id   |
| asin      | 商品asin |
| add_date  | 任务添加时间 |
| state     | 任务状态   |
#### -> 数据表字段

| id                      |          |
| ----------------------- | -------- |
| market_id               | 站点id     |
| data_date               | 数据插入日期   |
| parent_asin             | 父asin    |
| link                    | 链接       |
| brand                   | 品牌       |
| scribing_price          | 划线价      |
| selling_price           | 售价       |
| reviews_ratings         | 评论数量     |
| reviews_stars           | 评分       |
| customers_say           | 客户评论ai总结 |
| ships_from              | 始运地      |
| sold_by                 | 售卖者      |
| delivery_date_str       | 到货日期     |
| prime_delivery_date_str | 会员到货日期   |
| ld_progress             | LD秒杀进度   |
| coupon                  | 优惠信息     |
| product_info            | 商品参数     |
| about_this_item         | 关于此商品    |
| star_info               | 评级占比     |
| product_details         | 产品详情     |
| best_sellers_rank_main  | 大类热卖榜排名  |
| best_sellers_rank_child | 子类热卖榜排名  |
| is_reviews              | 是否有热门评论  |
| top_reviews             | 热门评论     |
| is_reviews_img          | 是否有带图评论  |
| reviews_with_images     | 带图评论     |
| category_tree           | 类目递归信息   |
| title                   | 商品标题     |
| main_image_url          | 主图链接     |
| in_stock                | 是否在售     |
| stock_description       | 库存描述     |
| created_at              | 创建时间     |
| seller_id               | 卖家id     |
| dim_asin                | 目标asin   |
| dimension_name          | 变体属性名    |
| dimension_values_data   | 变体属性名称列表 |
| child_asin              | 子asin    |
| product_description_img | 商品描述图片   |
### 三、商家详情

#### -> 任务表字段

| id        |        |
| --------- | ------ |
| market_id | 站点id   |
| seller_id | 卖家id   |
| add_date  | 任务添加时间 |
| state     | 任务状态   |
#### -> 数据表字段

| id               |      |
| ---------------- | ---- |
| market_id        | 站点id |
| seller_id        | 卖家id |
| seller_name      | 卖家名称 |
| url              | 链接   |
| business_name    | 公司名称 |
| business_address | 公司地址 |
| crawl_date       | 抓取时间 |
### 四、榜单

#### -> 任务表字段

| id          |                 |
| ----------- | --------------- |
| market_id   | 站点id            |
| url         | 榜单链接            |
| url_type    | 榜单类型（best或者new） |
| category_id | 类目id            |
| add_date    | 任务添加时间          |
| state       | 任务状态            |
#### -> 数据表字段

| id          |         |
| ----------- | ------- |
| market_id   | 站点id    |
| url         | 榜单链接    |
| seller_name | 卖家名称    |
| url_type    | 榜单类型    |
| asin        | asin    |
| img_url     | 商品图片url |
| title       | 标题      |
| stars       | 评分      |
| num         | 销量      |
| offers      | 优惠价格数量  |
| price       | 价格      |
| rank        | 排名      |
| crawl_date  | 抓取时间    |
### 五、带邮编cookie

#### -> 任务表
| id               |             |
| ---------------- | ----------- |
| market_id        | 站点id        |
| post_code        | 邮编          |
| num              | 需要的cookie数量 |
| valid_time_hours | 多少小时过期      |
| add_date         | 任务添加时间      |
| state            | 任务状态        |
#### -> 数据表（存入redis）

| key                                   | value        | TTL(过期时间)  |
| ------------------------------------- | ------------ | ---------- |
| cookie:market_id:post_code:crawl_date | cookie(dict) | valid_time |



  
## 爬虫逻辑  


| 模块     | 文件                          | 下发任务命令           | 采集命令             |
| ------ | --------------------------- | ---------------- | ---------------- |
| 搜索     | amazon_search_product.py    | xxx.py --start 1 | xxx.py --start 2 |
| 商品详情   | amazon_product_details.py   | xxx.py --start 1 | xxx.py --start 2 |
| 商家详情   | amazon_merchant_detail.py   | xxx.py --start 1 | xxx.py --start 2 |
| 榜单     | amazon_rank_list_product.py | xxx.py --start 1 | xxx.py --start 2 |
| cookie | amazon_cookies.py           | xxx.py --start 1 | xxx.py --start 2 |


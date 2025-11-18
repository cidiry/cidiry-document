



让我详细解析 `amazon_reviews.py` 文件，这是一个专门用于爬取亚马逊商品评论的爬虫。

### 1. 主要功能
```python
# 主要爬取内容：
- 商品评论内容
- 评分信息
- 评论者信息
- 评论时间
- 评论有用度
- 购买验证信息
```

### 2. 核心组件分析

#### 2.1 评论解析函数
```python
def parse_reviews(response, meta):
    _root = etree.HTML(response)
    review_list = _root.xpath("//div[@data-hook='review']")
    
    for review in review_list:
        try:
            item = {
                # 基础信息
                "task_id": meta.get("ds"),
                "asin": meta.get("keyword"),
                "marketplace_id": meta.get("market_place_id"),
                
                # 评论者信息
                "reviewer_name": parse_xpath(".//span[@class='a-profile-name']/text()", 
                                          review),
                "reviewer_id": parse_xpath(".//div[@data-hook='genome-widget']/@data-reviewer-id", 
                                         review),
                
                # 评论内容
                "review_title": parse_xpath(".//a[@data-hook='review-title']/span/text()", 
                                          review),
                "review_text": parse_xpath(".//span[@data-hook='review-body']/span/text()", 
                                         review),
                
                # 评分信息
                "rating": parse_xpath(".//i[@data-hook='review-star-rating']/span/text()", 
                                    review),
                
                # 评论元数据
                "review_date": parse_xpath(".//span[@data-hook='review-date']/text()", 
                                         review),
                "verified_purchase": 1 if parse_xpath(".//span[@data-hook='avp-badge']/text()", 
                                                    review) else 0,
                "helpful_votes": parse_xpath(".//span[@data-hook='helpful-vote-statement']/text()", 
                                           review),
                
                # 时间戳
                "created_at": datetime.now().strftime("%Y-%m-%d %H:%M:%S")
            }
            
            # 数据清洗和验证
            if clean_and_validate_review(item):
                await process_item(item)
                
        except Exception as e:
            logger.error(f"Error parsing review: {e}")
            continue
```

#### 2.2 异步爬取函数
```python
async def async_crawl_data(_data, _url):
    base_url = f"{_url}product-reviews/{_data['keyword']}"
    page = 1
    
    async with aiohttp.ClientSession() as session:
        while True:
            try:
                # 构建评论页URL
                params = {
                    "pageNumber": page,
                    "sortBy": "recent",  # 按最新排序
                    "reviewerType": "all_reviews"
                }
                url = f"{base_url}?{urlencode(params)}"
                
                response = await session.get(
                    url,
                    headers=REQUEST_HEADERS_REVIEWS,
                    cookies=REVIEW_COOKIES,
                    timeout=TIMEOUT
                )
                
                if response.status == 200:
                    html = await response.text()
                    
                    # 检查是否还有评论
                    if "no reviews" in html.lower():
                        break
                        
                    # 解析评论
                    await parse_reviews(html, _data)
                    
                    # 检查是否有下一页
                    if not has_next_page(html):
                        break
                        
                    page += 1
                    
                else:
                    logger.error(f"Error status code: {response.status}")
                    break
                    
            except Exception as e:
                logger.error(f"Error crawling reviews: {e}")
                break
```

#### 2.3 数据清洗函数
```python
def clean_and_validate_review(item):
    """清洗和验证评论数据"""
    try:
        # 处理评分
        if item['rating']:
            item['rating'] = float(item['rating'].split(' ')[0])
            
        # 处理日期
        if item['review_date']:
            item['review_date'] = parse_review_date(item['review_date'])
            
        # 处理有用度投票
        if item['helpful_votes']:
            item['helpful_votes'] = extract_numbers(item['helpful_votes'])
            
        # 清理评论文本
        if item['review_text']:
            item['review_text'] = clean_text(item['review_text'])
            
        # 验证必要字段
        required_fields = ['asin', 'review_text', 'rating']
        if not all(item.get(field) for field in required_fields):
            return False
            
        return True
        
    except Exception as e:
        logger.error(f"Error cleaning review: {e}")
        return False
```

#### 2.4 日期处理函数
```python
def parse_review_date(date_str):
    """处理评论日期"""
    try:
        # 处理不同格式的日期
        date_patterns = [
            "Reviewed in %s on %B %d, %Y",
            "Reviewed in %s on %d %B %Y",
            "%B %d, %Y"
        ]
        
        for pattern in date_patterns:
            try:
                return datetime.strptime(date_str, pattern).strftime("%Y-%m-%d")
            except ValueError:
                continue
                
        return None
        
    except Exception as e:
        logger.error(f"Error parsing date: {e}")
        return None
```

#### 2.5 分页检查函数
```python
def has_next_page(html):
    """检查是否有下一页评论"""
    try:
        _root = etree.HTML(html)
        next_button = _root.xpath("//li[@class='a-last']/a")
        return len(next_button) > 0
    except Exception as e:
        logger.error(f"Error checking pagination: {e}")
        return False
```

### 3. 错误处理机制

1. **请求重试**
```python
async def retry_request(session, url, max_retries=3):
    """请求重试机制"""
    for attempt in range(max_retries):
        try:
            async with session.get(url, headers=REQUEST_HEADERS_REVIEWS) as response:
                if response.status == 200:
                    return await response.text()
                elif response.status == 429:  # Too Many Requests
                    wait_time = 2 ** attempt  # 指数退避
                    await asyncio.sleep(wait_time)
                    continue
        except Exception as e:
            logger.error(f"Request error: {e}")
            if attempt == max_retries - 1:
                raise
```

### 4. 性能优化

1. **并发控制**
```python
# 限制并发请求数
semaphore = asyncio.Semaphore(MAX_CONCURRENT_REQUESTS)
async with semaphore:
    # 执行请求
```

2. **批量处理**
```python
# 批量保存评论
async def batch_save_reviews(reviews, batch_size=100):
    """批量保存评论数据"""
    for i in range(0, len(reviews), batch_size):
        batch = reviews[i:i + batch_size]
        await save_reviews_batch(batch)
```

### 5. 主要特点

1. **完整性**
- 支持全量评论抓取
- 包含评论所有元数据
- 支持多语言评论

2. **准确性**
- 严格的数据验证
- 完善的日期处理
- 文本清洗流程

3. **效率**
- 异步请求
- 批量处理
- 智能并发控制

4. **反爬处理**
- 请求间隔控制
- 代理IP支持
- Cookie管理

### 6. 使用场景

1. **产品分析**
- 评论情感分析
- 用户反馈收集
- 产品改进建议

2. **市场研究**
- 竞品评价分析
- 用户需求挖掘
- 市场趋势研究

3. **质量监控**
- 产品问题发现
- 服务质量监控
- 用户满意度跟踪

这个评论爬虫设计完善，功能全面，特别适合用于产品分析和市场研究等场景。它的错误处理和性能优化使其能够稳定地进行大规模评论数据采集。

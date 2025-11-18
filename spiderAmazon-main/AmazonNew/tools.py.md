
让我详细解释 `tools.py` 文件的各个组件：

### 1. 数字提取函数
```python
def extract_numbers(input_string):
    """提取字符串中的所有数字并返回一个列表"""
    numbers = []
    current_number = ''
    
    # 如果输入就是整数，直接返回
    if isinstance(input_string, int):
        return input_string

    # 处理字符串，移除逗号
    for char in input_string.replace(",", ""):
        if char.isdigit():
            current_number += char
        elif current_number:
            numbers.append(current_number)
            current_number = ''

    # 处理末尾数字
    if current_number:
        numbers.append(current_number)

    return int(numbers[0]) if numbers else 0
```

用途：
- 从文本中提取数字（如评论数、销量等）
- 处理带逗号的数字格式
- 返回第一个找到的数字

### 2. 浮点数提取函数
```python
def extract_first_float(input_string):
    """提取字符串中的第一个浮点数并返回"""
    current_number = ''
    found_decimal = False

    for char in input_string.replace(",", "."):
        if char.isdigit():
            current_number += char
        elif char == '.' and not found_decimal:
            current_number += char
            found_decimal = True
        elif current_number:
            break

    return float(current_number) if current_number else 0.0
```

用途：
- 提取价格
- 提取评分等浮点数
- 处理不同地区的小数点格式

### 3. XPath解析函数
```python
def parse_xpath(
        xpath_syntax: str,
        root: Any,
        start: int = 0,
        end: int = 0,
        merge: bool = False,
        merge_format: str = ""
) -> str:
    """解析XPath表达式并返回结果"""
    try:
        result = root.xpath(xpath_syntax)
        if result:
            if merge:
                _string = merge_format.join(result)
            else:
                _string = result[0] if len(result) > 0 else ""
                if start or end:
                    _string = _string[start:end]
        else:
            _string = ""

        return _string.strip().replace("\n", "").replace('"', "").replace("'", "").replace("  ", "")
    except IndexError:
        return ''
```

用途：
- 统一的XPath解析处理
- 支持文本合并
- 支持字符串切片
- 清理文本格式

### 4. 正则匹配函数
```python
def regex_match(pattern: str, text: bool or str) -> Union[str, bool]:
    """使用正则表达式匹配并返回结果"""
    if not text:
        return ''

    match = re.search(pattern, text)
    if match:
        return match.group(1).strip().replace("\n", "").replace('"', "").replace('\\', "")
    else:
        return ''
```

用途：
- 通用的正则表达式匹配
- 文本清理
- 提取特定格式的数据

### 5. 代理获取类
```python
class GetProxy:
    def __init__(self):
        self.r = redis.Redis(
            host=settings.REDIS_HOST,
            port=settings.REDIS_PORT,
            db=0,
            password=settings.REDIS_PASSWORD
        )

    @property
    def hw_ip(self):
        """获取华为代理IP"""
        ip = self.r.srandmember("hw_proxy_pool")
        return ip.decode('utf-8') if isinstance(ip, bytes) else ip

    @property
    def zh_ip(self):
        """获取其他代理IP"""
        ip = self.r.srandmember("zh_proxy_pool")
        return ip.decode('utf-8') if isinstance(ip, bytes) else ip
```

用途：
- 管理代理IP池
- 提供不同类型的代理IP
- 自动处理编码

### 6. SSL/TLS相关功能
```python
# ja3指纹
ORIGIN_CIPHERS = ('ECDH+AESGCM:DH+AESGCM:ECDH+AES256:DH+AES256:ECDH+AES128:DH+AES:ECDH+HIGH:'
                  'DH+HIGH:ECDH+3DES:DH+3DES:RSA+AESGCM:RSA+AES:RSA+HIGH:RSA+3DES')

def ciphers():
    """随机打乱加密方式"""
    _ciphers = copy.deepcopy(ORIGIN_CIPHERS)
    random.shuffle(_ciphers)
    _ciphers = ':'.join(_ciphers)
    _ciphers += ":!aNULL:!eNULL:!MD5"
    return _ciphers
```

### 7. HTTP客户端工具
```python:Amazon_new/spiders/tools.py
def aiohttp_ssl():
    """配置aiohttp的SSL上下文"""
    
def requests_session(domain: str):
    """配置requests会话"""
    
class DESAdapter(HTTPAdapter):
    """自定义的HTTP适配器"""
```

用途：
- 配置SSL/TLS设置
- 自定义HTTP客户端行为
- 防止指纹识别

### 主要特点：

1. **通用性**
   - 提供了常用的数据处理函数
   - 支持多种数据格式

2. **安全性**
   - 包含SSL/TLS配置
   - 支持代理管理

3. **可扩展性**
   - 模块化设计
   - 易于集成新功能

4. **错误处理**
   - 包含异常处理
   - 提供默认值

### 使用场景：

1. **数据清洗**
   - 提取数字和浮点数
   - 清理文本格式

2. **网页解析**
   - XPath解析
   - 正则匹配

3. **网络请求**
   - 代理管理
   - SSL配置
   - HTTP客户端配置

这个工具文件为爬虫项目提供了基础的工具函数和类，是整个项目的重要支持组件。

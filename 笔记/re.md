### 4. **三者的对比**

|方法|匹配范围|返回值类型|是否匹配开头|是否返回所有匹配|
|---|---|---|---|---|
|`re.match()`|字符串开头|`re.Match` 或 `None`|是|否|
|`re.search()`|整个字符串|`re.Match` 或 `None`|否|否|
|`re.findall()`|整个字符串|列表|否|是|

today = datetime.now().strftime('%Y-%m-%d')
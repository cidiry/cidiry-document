
`retry_on_exception` 和 `retry_on_result` 是 `retry` 装饰器中用于定义重试条件的两个关键参数。它们分别用于在**函数抛出异常**或**返回特定结果**时触发重试。以下是对这两个参数的详细讲解：

---

### **1. `retry_on_exception`**
#### **作用**
- 用于定义在函数抛出异常时是否触发重试。
- 如果函数抛出异常，并且 `retry_on_exception` 返回 `True`，则触发重试；否则，直接抛出异常。

#### **参数**
- **类型**：函数
- **函数签名**：`retry_on_exception(exception) -> bool`
  - `exception`：捕获到的异常对象。
  - 返回值：`True` 表示触发重试，`False` 表示不触发重试。

#### **示例**
```python
from retrying import retry

def retry_on_value_error(exception):
    """
    仅在捕获到 ValueError 时触发重试
    """
    return isinstance(exception, ValueError)

@retry(retry_on_exception=retry_on_value_error, stop_max_attempt_number=3)
def my_function():
    print("执行函数...")
    raise ValueError("模拟异常")

my_function()
```

**输出**：
```
执行函数...
执行函数...
执行函数...
Traceback (most recent call last):
  ...
ValueError: 模拟异常
```

**说明**：
- 当函数抛出 `ValueError` 时，`retry_on_value_error` 返回 `True`，触发重试。
- 重试 3 次后仍然失败，抛出异常。

---

#### **常见用法**
1. **捕获特定异常**：
   ```python
   def retry_on_specific_exceptions(exception):
       return isinstance(exception, (ValueError, TypeError))
   ```

2. **根据异常信息判断**：
   ```python
   def retry_on_exception_message(exception):
       return "retry" in str(exception)
   ```

3. **捕获所有异常**：
   ```python
   def retry_on_any_exception(exception):
       return True
   ```

---

### **2. `retry_on_result`**
#### **作用**
- 用于定义在函数返回特定结果时是否触发重试。
- 如果函数返回值满足 `retry_on_result` 的条件（返回 `True`），则触发重试；否则，直接返回结果。

#### **参数**
- **类型**：函数
- **函数签名**：`retry_on_result(result) -> bool`
  - `result`：函数的返回值。
  - 返回值：`True` 表示触发重试，`False` 表示不触发重试。

#### **示例**
```python
from retrying import retry

def retry_if_result_is_none(result):
    """
    仅在返回值为 None 时触发重试
    """
    return result is None

@retry(retry_on_result=retry_if_result_is_none, stop_max_attempt_number=3)
def my_function():
    print("执行函数...")
    return None  # 模拟返回 None

my_function()
```

**输出**：
```
执行函数...
执行函数...
执行函数...
Traceback (most recent call last):
  ...
retrying.RetryError: RetryError[<Future at 0x7f8b1c0b6d90 state=finished raised RetryError>]
```

**说明**：
- 当函数返回 `None` 时，`retry_if_result_is_none` 返回 `True`，触发重试。
- 重试 3 次后仍然返回 `None`，抛出 `RetryError`。

---

#### **常见用法**
1. **返回特定值时重试**：
   ```python
   def retry_if_result_is_false(result):
       return result is False
   ```

2. **返回错误码时重试**：
   ```python
   def retry_if_error_code(result):
       return result.get("code") == 500
   ```

3. **返回空列表时重试**：
   ```python
   def retry_if_empty_list(result):
       return isinstance(result, list) and len(result) == 0
   ```

---

### **3. `retry_on_exception` 和 `retry_on_result` 的区别**
| 特性                  | `retry_on_exception`                          | `retry_on_result`                          |
|-----------------------|-----------------------------------------------|--------------------------------------------|
| **触发条件**          | 函数抛出异常时触发                            | 函数返回特定结果时触发                     |
| **参数类型**          | 异常对象                                      | 函数返回值                                 |
| **返回值**            | `True` 表示触发重试，`False` 表示不触发重试   | `True` 表示触发重试，`False` 表示不触发重试 |
| **适用场景**          | 处理函数执行过程中可能出现的异常              | 处理函数返回值不符合预期的情况             |

---

### **4. 组合使用 `retry_on_exception` 和 `retry_on_result`**
可以同时使用 `retry_on_exception` 和 `retry_on_result`，分别处理异常和返回值。

#### **示例**
```python
from retrying import retry

def retry_on_value_error(exception):
    return isinstance(exception, ValueError)

def retry_if_result_is_none(result):
    return result is None

@retry(
    retry_on_exception=retry_on_value_error,
    retry_on_result=retry_if_result_is_none,
    stop_max_attempt_number=3
)
def my_function():
    print("执行函数...")
    if random.random() < 0.5:
        raise ValueError("模拟异常")
    return None  # 模拟返回 None

my_function()
```

**输出**：
- 如果抛出 `ValueError`，触发重试。
- 如果返回 `None`，触发重试。
- 重试 3 次后仍然失败，抛出异常或 `RetryError`。

---

### **总结**
- **`retry_on_exception`**：用于在函数抛出异常时触发重试，适合处理异常场景。
- **`retry_on_result`**：用于在函数返回特定结果时触发重试，适合处理返回值不符合预期的场景。
- 两者可以单独使用，也可以组合使用，实现更灵活的重试逻辑。
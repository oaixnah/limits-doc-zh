---
hide:
  - navigation
description: 学习如何为Python limits包创建自定义存储后端，支持固定窗口、移动窗口和滑动窗口计数器等速率限制策略。文档提供完整示例代码，展示如何实现存储注册表模式，让您轻松扩展存储功能而无需修改包本身。
---

# 自定义存储后端

**limits** 包附带了一些存储实现，让您可以开始使用一些常见的用于速率限制的数据存储（Redis 和 Memcached）。

为了适应默认存储后端的定制或完全不同的存储后端，**limits** 使用了一种注册表模式，使得添加您自己的自定义存储变得轻松（无需向包本身提交补丁）。

创建自定义后端需要：

1. 子类化 `limits.storage.Storage` 或 `limits.aio.storage.Storage` 并实现抽象方法。这将允许存储与 `Fixed Window` 策略一起使用。
2. 如果存储可以支持 `Moving Window` 策略 – 额外实现来自 `MovingWindowSupport` 的方法
3. 如果存储可以支持 `Sliding Window Counter` 策略 – 额外实现来自 `SlidingWindowCounterSupport` 的方法
4. 提供命名 *schemes*，可用于在存储注册表中查找自定义存储。（有关详细信息，请参阅 `Storage Scheme`）

## 示例

以下示例展示了两个后端存储：一个仅支持 `Fixed Window` 策略，另一个实现了所有策略。注意 `STORAGE_SCHEME` 类
变量，这些变量导致类被注册到 **limits** 存储注册表中：

```python
import time
from urllib.parse import urlparse
from typing import Tuple, Type, Union
from limits.storage import Storage, MovingWindowSupport, SlidingWindowCounterSupport


class BasicStorage(Storage):
    """一个简单的固定窗口存储后端。"""
    STORAGE_SCHEME = ["basicdb"]

    def __init__(self, uri: str, **options) -> None:
        self.host = urlparse(uri).hostname or ""
        self.port = urlparse(uri).port or 0

    @property
    def base_exceptions(self) -> Union[Type[Exception], Tuple[Type[Exception], ...]]:
        return ()

    def check(self) -> bool:
        return True

    def get_expiry(self, key: str) -> int:
        return int(time.time())

    def incr(self, key: str, expiry: int, elastic_expiry: bool = False, amount: int = 1) -> int:
        return amount

    def get(self, key: str) -> int:
        return 0

    def reset(self) -> int:
        return 0

    def clear(self, key: str) -> None:
        pass

    
class AdvancedStorage(Storage, MovingWindowSupport, SlidingWindowCounterSupport):
    """一个更高级的存储后端，支持所有速率限制策略。"""
    STORAGE_SCHEME = ["advanceddatabase"]

    def __init__(self, uri: str, **options) -> None:
        self.host = urlparse(uri).hostname or ""
        self.port = urlparse(uri).port or 0

    @property
    def base_exceptions(self) -> Union[Type[Exception], Tuple[Type[Exception], ...]]:
        return ()

    def check(self) -> bool:
        return True

    def get_expiry(self, key: str) -> int:
        return int(time.time())

    def incr(self, key: str, expiry: int, elastic_expiry: bool = False, amount: int = 1) -> int:
        return amount

    def get(self, key: str) -> int:
        return 0

    def reset(self) -> int:
        return 0

    def clear(self, key: str) -> None:
        pass

    # --- 移动窗口支持 ---
    def acquire_entry(self, key: str, limit: int, expiry: int, amount: int = 1) -> bool:
        return True

    def get_moving_window(self, key: str, limit: int, expiry: int) -> Tuple[float, int]:
        return (time.time(), 0)

    # --- 滑动窗口计数器支持 ---
    def acquire_sliding_window_entry(self, key: str, limit: int, expiry: int, amount: int = 1) -> bool:
        return True

    def get_sliding_window(self, key: str, expiry: int) -> Tuple[int, float, int, float]:
        return (0, expiry / 2, 0, expiry)
```

一旦声明了上述实现，您可以使用 `Storage Factory function` 以以下方式查找它们：

```python
from limits.storage import storage_from_string


basic_store = storage_from_string("basicdb://localhost:42")
advanced_store = storage_from_string("advanceddatabase://localhost:42")
```

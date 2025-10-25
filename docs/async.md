---
hide:
  - navigation
description: 了解limits库的异步变体，包括Redis、Memcached、MongoDB等异步存储后端和移动窗口速率限制策略的完整实现指南。
---

# 异步支持

命名空间 `limits.aio` 镜像了 `limits.storage` 和 `limits.strategies` 的异步变体。

已实现以下异步存储后端：

- 内存存储
- Redis（通过 [coredis](https://coredis.readthedocs.org)
  或 [redis-py](https://redis-py.readthedocs.io)。请参考
  `limits.aio.storage.RedisStorage.implementation` 了解
  选择依赖项的详细信息）
- Memcached（通过 [memcachio](https://memcachio.readthedocs.org)）
- MongoDB（通过 [motor](https://motor.readthedocs.org)）

## 快速开始

此示例演示了 `limits.aio` 命名空间中的细微差异：

```python
from limits import parse
from limits.storage import storage_from_string
from limits.aio.strategies import MovingWindowRateLimiter

redis = storage_from_string("async+redis://localhost:6379")

moving_window = MovingWindowRateLimiter(redis)
one_per_minute = parse("1/minute")


async def hit():
    return await moving_window.hit(one_per_minute, "test_namespace", "foo")
```

有关异步存储后端的更多实现细节，请参考 `Async Storage`，
有关异步速率限制策略 API，请参考 `Async Strategies`。

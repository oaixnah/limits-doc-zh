---
hide:
  - navigation
description: Python limits库快速入门指南 - 学习如何配置和使用速率限制功能，包括内存、Memcached、Redis存储后端，以及固定窗口、移动窗口、滑动窗口计数器等策略，提供完整的代码示例和测试方法。
---

# 快速开始

!!! note

    在异步上下文中，只需将 `limits` 的导入替换为 `limits.aio`，并在存储和限制器方法中使用 `await`。

## 初始化策略和存储

### 初始化存储后端

=== "内存存储"

    ``` python
    from limits import storage
    limits_storage = storage.MemoryStorage()
    ```

=== "Memcached"

    ``` python
    from limits import storage
    limits_storage = storage.MemcachedStorage(
        "memcached://localhost:11211"
    )
    ```

=== "Redis"

    ``` python
    from limits import storage
    limits_storage = storage.RedisStorage("redis://localhost:6379/1")
    ```

### 初始化速率限制器

=== "使用固定窗口策略"

    ``` python
    from limits import strategies
    limiter = strategies.FixedWindowRateLimiter(limits_storage)
    ```

=== "使用移动窗口策略"

    !!! warning
        如果使用的存储不支持移动窗口策略，将会抛出 `NotImplementedError` 异常

    ``` python
    from limits import strategies
    limiter = strategies.MovingWindowRateLimiter(limits_storage)
    ```

=== "使用滑动窗口计数器策略"

    !!! warning
        如果使用的存储不支持滑动窗口计数器策略，将会抛出 :exc:`NotImplementedError` 异常

    ``` python
    from limits import strategies
    limiter = strategies.SlidingWindowCounterRateLimiter(limits_storage)
    ```

## 描述速率限制

### 使用字符串表示法初始化速率限制

``` python
from limits import parse
one_per_minute = parse("1/minute")
```

### 使用 `RateLimitItem` 子类显式初始化速率限制

``` python
from limits import RateLimitItemPerSecond
one_per_second = RateLimitItemPerSecond(1, 1)
```

## 测试限制

### 消耗限制

``` shell
>>> limiter.hit(one_per_minute, "test_namespace", "foo")
True
>>> limiter.hit(one_per_minute, "test_namespace", "foo")
False
>>> limiter.hit(one_per_minute, "test_namespace", "bar")
True

>>> limiter.hit(one_per_second, "test_namespace", "foo")
True
>>> limiter.hit(one_per_second, "test_namespace", "foo")
False
>>> time.sleep(1)
>>> limiter.hit(one_per_second, "test_namespace", "foo")
True
```

### 检查而不消耗

``` shell
>>> limiter.hit(one_per_second, "test_namespace", "foo")
True
>>> while not limiter.test(one_per_second, "test_namespace", "foo"):
...     time.sleep(0.01)
>>> limiter.hit(one_per_second, "test_namespace", "foo")
True
```

### 查询可用容量和重置时间

``` shell
>>> limiter.hit(one_per_minute, "test_namespace", "foo")
True
>>> window = limiter.get_window_stats(one_per_minute, "test_namespace", "foo")
>>> window.remaining
0
>>> limiter.hit(one_per_minute, "test_namespace", "foo")
False
>>> time.sleep(window.reset_time - time.time())
>>> limiter.hit(one_per_minute, "test_namespace", "foo")
True
```

## 清除限制

``` shell
>>> limiter.hit(one_per_minute, "test_namespace", "foo")
True
>>> limiter.hit(one_per_minute, "test_namespace", "foo")
False
>>> limiter.clear(one_per_minute, "test_namespace", "foo")
>>> limiter.hit(one_per_minute, "test_namespace", "foo")
True
```

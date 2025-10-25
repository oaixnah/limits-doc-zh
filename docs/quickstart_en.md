---
hide:
  - navigation
---

# Quickstart

!!! note

    In an async context, simply substitute the imports from `limits` to `limits.aio` and use `await` in the storage and limiter methods.


## Initialize the strategy & storage

### Initialize the storage backend

=== "In Memory"

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

### Initialize a rate limiter

=== "With the Fixed window strategy"

    ``` python
    from limits import strategies
    limiter = strategies.FixedWindowRateLimiter(limits_storage)
    ```

=== "With the Moving window strategy"

    !!! warning
        If the storage used does not support the moving window
        strategy, `NotImplementedError` will be raised

    ``` python
    from limits import strategies
    limiter = strategies.MovingWindowRateLimiter(limits_storage)
    ```

=== "With the Sliding window counter strategy"

    !!! warning
        If the storage used does not support the sliding window
        counter strategy, :exc:`NotImplementedError` will be raised

    ``` python
    from limits import strategies
    limiter = strategies.SlidingWindowCounterRateLimiter(limits_storage)
    ```

## Describe the rate limit

### Initialize a rate limit using the `string notation`

``` python
from limits import parse
one_per_minute = parse("1/minute")
```

### Initialize a rate limit explicitly using a subclass of `RateLimitItem`

``` python
from limits import RateLimitItemPerSecond
one_per_second = RateLimitItemPerSecond(1, 1)
```


## Test the limits

### Consume the limits

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

### Check without consuming

``` shell
>>> limiter.hit(one_per_second, "test_namespace", "foo")
True
>>> while not limiter.test(one_per_second, "test_namespace", "foo"):
...     time.sleep(0.01)
>>> limiter.hit(one_per_second, "test_namespace", "foo")
True
```

### Query available capacity and reset time

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

## Clear a limit

``` shell
>>> limiter.hit(one_per_minute, "test_namespace", "foo")
True
>>> limiter.hit(one_per_minute, "test_namespace", "foo")
False
>>> limiter.clear(one_per_minute, "test_namespace", "foo")
>>> limiter.hit(one_per_minute, "test_namespace", "foo")
True
```

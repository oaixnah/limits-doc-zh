---
hide:
  - navigation
description: limits库安装指南 - 支持Redis、Memcached、MongoDB、Valkey等多种存储后端，包含同步和异步版本安装说明，使用uv或pip轻松安装Python限流工具。
---

# 安装

安装包

``` shell
# 使用 uv
uv add limits

# 使用 pip
pip install limits
```

## 存储

=== "Redis"

    ``` shell
    # 使用 uv
    uv add limits[redis]

    # 使用 pip
    pip install limits[redis]
    ```

    包含

    ```
    redis>3,!=4.5.2,!=4.5.3,<6.0.0
    ```

=== "RedisCluster"

    ``` shell
    # 使用 uv
    uv add limits[rediscluster]

    # 使用 pip
    pip install limits[rediscluster]
    ```

    包含

    ```
    redis>=4.2.0,!=4.5.2,!=4.5.3
    ```

=== "Memcached"

    ``` shell
    # 使用 uv
    uv add limits[memcached]

    # 使用 pip
    pip install limits[memcached]
    ```

    包含

    ```
    pymemcache>3,<5.0.0
    ```

=== "MongoDB"

    ``` shell
    # 使用 uv
    uv add limits[mongodb]

    # 使用 pip
    pip install limits[mongodb]
    ```

    包含

    ```
    pymongo>4.1,<5
    ```

=== "Valkey"

    ``` shell
    # 使用 uv
    uv add limits[valkey]

    # 使用 pip
    pip install limits[valkey]
    ```

    包含

    ```
    valkey>=6
    ```

有关每个存储后端的具体详细信息，请参阅 `存储后端`

## 异步存储

如果您正在使用异步代码库，可以使用以下附加项安装存储依赖项：

=== "Redis"

    ``` shell
    # 使用 uv
    uv add limits[async-redis]

    # 使用 pip
    pip install limits[async-redis]
    ```

    包含：

    ```
    coredis>=3.4.0,<5
    ```

    > 在版本 4.2 中添加：如果安装了 `redis`，可以通过设置
    > `implementation` 为 `redispy` 来替代 `coredis`。
    > 有关更多详细信息，请参阅 `limits.aio.storage.RedisStorage`。

=== "Memcached"

    ``` shell
    # 使用 uv
    uv add limits[async-memcached]

    # 使用 pip
    pip install limits[async-memcached]
    ```

    包含：

    ```
    memcachio>=0.3
    ```

    > 在版本 5.0 中更改：如果安装了 `emcache`，可以通过设置 `implementation` 为 `emcache` 来替代新的默认值 `memcachio`。


=== "MongoDB"

    ``` shell
    # 使用 uv
    uv add limits[async-mongodb]

    # 使用 pip
    pip install limits[async-mongodb]
    ```

    包含：

    ```
    motor>=3,<4
    ```

=== "Valkey"

    ``` shell
    # 使用 uv
    uv add limits[async-valkey]

    # 使用 pip
    pip install limits[async-valkey]
    ```

    包含：

    ```
    valkey>=6
    ```

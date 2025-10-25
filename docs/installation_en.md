---
hide:
  - navigation
---

# Installation

Install the package

``` shell
# Using uv
uv add limits

# Using pip
pip install limits
```

## Storage

=== "Redis"

    ``` shell
    # Using uv
    uv add limits[redis]

    # Using pip
    pip install limits[redis]
    ```

    Includes

    ```
    redis>3,!=4.5.2,!=4.5.3,<6.0.0
    ```

=== "RedisCluster"

    ``` shell
    # Using uv
    uv add limits[rediscluster]

    # Using pip
    pip install limits[rediscluster]
    ```

    Includes

    ```
    redis>=4.2.0,!=4.5.2,!=4.5.3
    ```

=== "Memcached"

    ``` shell
    # Using uv
    uv add limits[memcached]

    # Using pip
    pip install limits[memcached]
    ```

    Includes

    ```
    pymemcache>3,<5.0.0
    ```

=== "MongoDB"

    ``` shell
    # Using uv
    uv add limits[mongodb]

    # Using pip
    pip install limits[mongodb]
    ```

    Includes

    ```
    pymongo>4.1,<5
    ```

=== "Valkey"

    ``` shell
    # Using uv
    uv add limits[valkey]

    # Using pip
    pip install limits[valkey]
    ```

    Includes

    ```
    valkey>=6
    ```

More details around the specifics of each storage backend can be found in `Storage Backends`


## Async Storage

If you are using an async code base you can install the storage dependencies along with the package using the following extras:


=== "Redis"

    ``` shell
    # Using uv
    uv add limits[async-redis]

    # Using pip
    pip install limits[async-redis]
    ```

    Includes:

    ```
    coredis>=3.4.0,<5
    ```

    > Added in version 4.2: `redis` if installed can be used instead of `coredis` by setting
    > `implementation` to `redispy`.
    > See `limits.aio.storage.RedisStorage` for more details.


=== "Memcached"

    ``` shell
    # Using uv
    uv add limits[async-memcached]

    # Using pip
    pip install limits[async-memcached]
    ```

    Includes:

    ```
    memcachio>=0.3
    ```

    > Changed in version 5.0: `emcache` if installed can be used instead of the new default
    > `memcachio` by setting `implementation` to `emcache`.


=== "MongoDB"

    ``` shell
    # Using uv
    uv add limits[async-mongodb]

    # Using pip
    pip install limits[async-mongodb]
    ```

    Includes:

    ```
    motor>=3,<4
    ```

=== "Valkey"

    ``` shell
    # Using uv
    uv add limits[async-valkey]

    # Using pip
    pip install limits[async-valkey]
    ```

    Includes:

    ```
    valkey>=6
    ```


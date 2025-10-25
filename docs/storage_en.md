---
hide:
  - navigation
---

# Storage Backends

## Supported versions

**limits** is tested and known to work with the following versions of the
dependency libraries and the associated storage versions.

The CI tests against these versions on a nightly basis and you can see
the results in [github](https://github.com/alisaifee/limits/actions/workflows/compatibility.yml).

=== "Redis"

    Dependency versions:

    ```
    redis>3,!=4.5.2,!=4.5.3,<6.0.0
    ```

    Dependency versions (async):

    ```
    coredis>=3.4.0,<5
    ```

    > Added in version 4.2: `redis` if installed can be used instead of `coredis` by setting
    > `implementation` to `redispy`.
    > See `limits.aio.storage.RedisStorage.implementatio` for more details.

    [Redis Server](https://redis.io){target="_blank"}

    ```
    7.0
    7.2
    7.4
    ```

=== "RedisCluster"

    Dependency versions:

    ```
    redis>=4.2.0,!=4.5.2,!=4.5.3
    ```

    Dependency versions (async):

    ```
    rediscluster>=4.2.0,!=4.5.2,!=4.5.3
    ```

    > Added in version 4.2: `redis` if installed can be used instead of `coredis` by setting
    > `implementation` to `redispy`.
    > See `limits.aio.storage.RedisClusterStorage.implementatio` for more details.

    [Redis Server](https://redis.io){target="_blank"}

    ```
    7.0
    7.2
    7.4
    ```

=== "Memcached"

    Dependency versions:

    ```
    pymemcache>3,<5.0.0
    ```

    Dependency versions (async):

    ```
    memcachio>=0.3
    ```

    [Memcached](https://memcached.org/){target="_blank"}

    ```
    1.5.16
    1.6.6
    1.6.15
    ```

=== "MongoDB"

    Dependency versions:

    ```
    pymongo>4.1,<5
    ```

    Dependency versions (async):

    ```
    motor>=3,<4
    ```

    [MongoDB](https://www.mongodb.com/){target="_blank"}

    ```
    5.0
    6.0
    7.0
    8.0
    ```

=== "Valkey"

    Dependency versions:

    ```
    valkey>=6
    ```

    Dependency versions (async):

    ```
    valkeyio>=6
    ```

    [Valkey](https://valkey.io/){target="_blank"}

    ```
    7.2
    8.0
    ```

## Storage scheme

**limits** uses a url style storage scheme notation (similar to the JDBC driver
connection string notation) for configuring and initializing
storage backends. This notation additionally provides a simple
mechanism to both identify and configure the backend implementation based on
a single string argument.

The storage scheme follows the format `{scheme}://{parameters}`

`limits.storage.storage_from_string()` is provided to
lookup and construct an instance of a storage based on the storage scheme. For example:

```python
import limits.storage


uri = "redis://localhost:9999"
options = {}
redis_storage = limits.storage.storage_from_string(uri, **options)
```

The additional `options` key-word arguments are passed as is to the constructor of the storage
and handled differently by each implementation. Please refer to the API documentation in
the `Storage` section for details.

## Examples

### In-Memory Storage

The in-memory storage (`MemoryStorage`) takes no parameters so the only relevant value is `memory://`

### Memcached Storage

Requires the location of the memcached server(s). As such
the parameters is a comma separated list of `{host}:{port}` locations such as
`memcached://localhost:11211` or
`memcached://localhost:11211,localhost:11212,192.168.1.1:11211` etc...
or a path to a unix domain socket such as `memcached:///var/tmp/path/to/sock`

Depends on: `pymemcache`

### Redis Storage

Requires the location of the redis server and optionally the database number.
`redis://localhost:6379` or `redis://localhost:6379/n` (for database `n`).

If the redis server is listening over a unix domain socket you can use `redis+unix:///path/to/sock`
or `redis+unix:///path/to/socket?db=n` (for database `n`).

If the database is password protected the password can be provided in the url, for example
`redis://:foobared@localhost:6379` or `redis+unix://:foobered/path/to/socket` if using a UDS.

For scenarios where a redis connection pool is already available and can be reused, it can be provided
in `options`, for example:

```python
pool = redis.connections.BlockingConnectionPool.from_url("redis://.....")
storage_from_string("redis://", connection_pool=pool)
```

Depends on: `redis`


> Added in version 4.3: If the database `uri` scheme uses `valkey` instead of `redis` the implementation
> used will be from `valkey` instead of `redis`.

### Redis+SSL Storage

The official Redis client `redis` supports redis connections over SSL with the scheme
You can add ssl related parameters in the url itself, for example:
`rediss://localhost:6379/0?ssl_ca_certs=./tls/ca.crt&ssl_keyfile=./tls/client.key`.


Depends on: `redis`

### Redis+Sentinel Storage

Requires the location(s) of the redis sentinel instances and the `service-name`
that is monitored by the sentinels.
`redis+sentinel://localhost:26379/my-redis-service`
or `redis+sentinel://localhost:26379,localhost:26380/my-redis-service`.

If the sentinel is password protected the username and/or password can be provided in the url,
for example  `redis+sentinel://:sekret@localhost:26379/my-redis-service`

When authentication details are provided in the url they will be used for both the sentinel
and as connection arguments for the underlying redis nodes managed by the sentinel.

If you need fine grained control it is recommended to use the additional `options`
arguments. More details can be found in the API documentation for `RedisSentinelStorage` (or the async version: `RedisSentinelStorage`).

Depends on: `redis`

### Redis Cluster Storage

Requires the location(s) of the redis cluster startup nodes (One is enough).
`redis+cluster://localhost:7000`
or `redis+cluster://localhost:7000,localhost:7001`

If the cluster is password protected the username and/or password can be provided in the url,
for example  `redis+cluster://:sekret@localhost:7000,localhost:7001`

Depends on: `redis`

### MongoDB Storage

Requires the location(s) of a mongodb installation using the uri schema
described by the [Mongodb URI Specification](https://github.com/mongodb/specifications/blob/master/source/uri-options/uri-options.md)

Examples:

  - Local instance: `mongodb://localhost:27017/`
  - Instance with SSL: `mongodb://mymongo.com/?tls=true`
  - Local instance with SSL & self signed/invalid certificate: `mongodb://localhost:27017/?tls=true&tlsAllowInvalidCertificates=true`

Depends on: `pymongo`

## Async Storage

When using limits in an async code base the same uri schema can be used
to query for an async implementation of the storage by prefixing the
scheme with `async+`.

For example:

- `async+redis://localhost:6379/0`
- `async+rediss://localhost:6379/0`
- `async+redis+cluster://localhost:7000,localhost:7001`
- `async+redis+sentinel://:sekret@localhost:26379/my-redis-service`
- `async+memcached://localhost:11211`
- `async+mongodb://localhost:27017`
- `async+memory://`

For implementation details of currently supported async backends refer to `Async Storage`
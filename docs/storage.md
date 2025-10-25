---
hide:
  - navigation
description: limits存储后端文档 - 支持Redis、Memcached、MongoDB、Valkey等多种存储方案，包含同步和异步版本配置指南、依赖版本要求和连接字符串示例。
---

# 存储后端

## 支持的版本

**limits** 经过测试，已知可与以下版本的依赖库和相关的存储版本配合使用。

CI 每晚都会对这些版本进行测试，您可以在 [github](https://github.com/alisaifee/limits/actions/workflows/compatibility.yml) 查看结果。

=== "Redis"

    依赖版本：

    ```
    redis>3,!=4.5.2,!=4.5.3,<6.0.0
    ```

    依赖版本（异步）：

    ```
    coredis>=3.4.0,<5
    ```

    > 在版本 4.2 中添加：如果安装了 `redis`，可以通过设置 `implementation` 为 `redispy` 来替代 `coredis`。
    > 更多详情请参阅 `limits.aio.storage.RedisStorage.implementatio`。

    [Redis Server](https://redis.io){target="_blank"}

    ```
    7.0
    7.2
    7.4
    ```

=== "RedisCluster"

    依赖版本：

    ```
    redis>=4.2.0,!=4.5.2,!=4.5.3
    ```

    依赖版本（异步）：

    ```
    rediscluster>=4.2.0,!=4.5.2,!=4.5.3
    ```

    > 在版本 4.2 中添加：如果安装了 `redis`，可以通过设置 `implementation` 为 `redispy` 来替代 `coredis`。
    > 更多详情请参阅 `limits.aio.storage.RedisClusterStorage.implementatio`。

    [Redis Server](https://redis.io){target="_blank"}

    ```
    7.0
    7.2
    7.4
    ```

=== "Memcached"

    依赖版本：

    ```
    pymemcache>3,<5.0.0
    ```

    依赖版本（异步）：

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

    依赖版本：

    ```
    pymongo>4.1,<5
    ```

    依赖版本（异步）：

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

    依赖版本：

    ```
    valkey>=6
    ```

    依赖版本（异步）：

    ```
    valkeyio>=6
    ```

    [Valkey](https://valkey.io/){target="_blank"}

    ```
    7.2
    8.0
    ```

## 存储方案

**limits** 使用类似 URL 风格的存储方案表示法（类似于 JDBC 驱动程序的连接字符串表示法）来配置和初始化存储后端。这种表示法还提供了一种简单的机制，可以基于单个字符串参数来识别和配置后端实现。

存储方案遵循格式 `{scheme}://{parameters}`

提供了 `limits.storage.storage_from_string()` 来根据存储方案查找和构造存储实例。例如：

```python
import limits.storage


uri = "redis://localhost:9999"
options = {}
redis_storage = limits.storage.storage_from_string(uri, **options)
```

额外的 `options` 关键字参数会原样传递给存储的构造函数，并由每个实现以不同方式处理。请参阅 `Storage` 部分的 API 文档以获取详细信息。

## 示例

### 内存存储

内存存储（`MemoryStorage`）不需要参数，因此唯一相关的值是 `memory://`

### Memcached 存储

需要 memcached 服务器的位置。因此参数是以逗号分隔的 `{host}:{port}` 位置列表，例如
`memcached://localhost:11211` 或
`memcached://localhost:11211,localhost:11212,192.168.1.1:11211` 等...
或者是 Unix 域套接字的路径，例如 `memcached:///var/tmp/path/to/sock`

依赖：`pymemcache`

### Redis 存储

需要 Redis 服务器的位置，可选择性地指定数据库编号。
`redis://localhost:6379` 或 `redis://localhost:6379/n`（对于数据库 `n`）。

如果 Redis 服务器通过 Unix 域套接字监听，您可以使用 `redis+unix:///path/to/sock`
或 `redis+unix:///path/to/socket?db=n`（对于数据库 `n`）。

如果数据库受密码保护，可以在 URL 中提供密码，例如
`redis://:foobared@localhost:6379` 或使用 UDS 时的 `redis+unix://:foobered/path/to/socket`。

对于已经存在且可以重用的 Redis 连接池场景，可以在 `options` 中提供，例如：

```python
pool = redis.connections.BlockingConnectionPool.from_url("redis://.....")
storage_from_string("redis://", connection_pool=pool)
```

依赖：`redis`

> 在版本 4.3 中添加：如果数据库 `uri` 方案使用 `valkey` 而不是 `redis`，则使用的实现将来自 `valkey` 而不是 `redis`。

### Redis+SSL 存储

官方的 Redis 客户端 `redis` 支持通过 SSL 的 Redis 连接，使用方案
您可以在 URL 本身中添加 SSL 相关参数，例如：
`rediss://localhost:6379/0?ssl_ca_certs=./tls/ca.crt&ssl_keyfile=./tls/client.key`。

依赖：`redis`

### Redis+Sentinel 存储

需要 Redis Sentinel 实例的位置和由 sentinel 监控的 `service-name`。
`redis+sentinel://localhost:26379/my-redis-service`
或 `redis+sentinel://localhost:26379,localhost:26380/my-redis-service`。

如果 Sentinel 受密码保护，可以在 URL 中提供用户名和/或密码，
例如 `redis+sentinel://:sekret@localhost:26379/my-redis-service`

当在 URL 中提供认证详细信息时，它们将同时用于 Sentinel 和作为由 Sentinel 管理的底层 Redis 节点的连接参数。

如果您需要细粒度控制，建议使用额外的 `options` 参数。更多详细信息可以在 `RedisSentinelStorage`（或异步版本：`RedisSentinelStorage`）的 API 文档中找到。

依赖：`redis`

### Redis Cluster 存储

需要 Redis 集群启动节点的位置（一个就足够了）。
`redis+cluster://localhost:7000`
或 `redis+cluster://localhost:7000,localhost:7001`

如果集群受密码保护，可以在 URL 中提供用户名和/或密码，
例如 `redis+cluster://:sekret@localhost:7000,localhost:7001`

依赖：`redis`

### MongoDB 存储

需要使用 [Mongodb URI 规范](https://github.com/mongodb/specifications/blob/master/source/uri-options/uri-options.md) 中描述的 URI 模式来指定 MongoDB 安装的位置。

示例：

  - 本地实例：`mongodb://localhost:27017/`
  - 带 SSL 的实例：`mongodb://mymongo.com/?tls=true`
  - 带 SSL 和自签名/无效证书的本地实例：`mongodb://localhost:27017/?tls=true&tlsAllowInvalidCertificates=true`

依赖：`pymongo`

## 异步存储

在异步代码库中使用 limits 时，可以使用相同的 URI 模式，通过在方案前添加 `async+` 前缀来查询存储的异步实现。

例如：

- `async+redis://localhost:6379/0`
- `async+rediss://localhost:6379/0`
- `async+redis+cluster://localhost:7000,localhost:7001`
- `async+redis+sentinel://:sekret@localhost:26379/my-redis-service`
- `async+memcached://localhost:11211`
- `async+mongodb://localhost:27017`
- `async+memory://`

有关当前支持的异步后端的实现详细信息，请参阅 `Async Storage`。

---
hide:
  - navigation
description: limits是一个Python速率限制库，支持多种策略（固定窗口、滑动窗口、令牌桶）和存储后端（Redis、Memcached、MongoDB），为同步和异步代码提供统一API，适用于Flask、Django、Sanic等框架的速率限制需求。  
---

# limits

![https://github.com/alisaifee/limits](https://img.shields.io/github/last-commit/alisaifee/limits?logo=github&style=for-the-badge&labelColor=#282828)
![https://github.com/alisaifee/limits/actions/workflows/main.yml](https://img.shields.io/github/actions/workflow/status/alisaifee/limits/main.yml?logo=github&style=for-the-badge&labelColor=#282828)
![https://github.com/alisaifee/limits](https://img.shields.io/codecov/c/github/alisaifee/limits?logo=codecov&style=for-the-badge&labelColor=#282828)
![https://pypi.org/project/limits](https://img.shields.io/pypi/v/limits?style=for-the-badge&logo=pypi)

**limits** 是一个用于通过多种策略进行速率限制的 Python 库，支持常用的存储后端（Redis、Memcached 和 MongoDB）。

该库为同步和 `async` 代码库提供了相同的 API。

请查看 `安装` 和 `快速开始` 来开始使用。

要了解更多关于不同策略的信息，请参考 `限速策略` 部分。

有关支持的后端概述，请参考 `存储后端`。

## 开发

源代码可在 [Github](https://github.com/alisaifee/limits) 上获取。

开始使用

``` shell
git clone https://github.com/alisaifee/limits.git
cd limits
pip install -r requirements/dev.txt
```

由于 `limits` 集成了各种后端存储，本地开发和运行测试需要安装可用的 [docker 和 docker-compose](https://docs.docker.com/compose/gettingstarted/)。

运行测试将自动启动相关容器 - 但会让它们保持运行状态，以避免每次测试运行时启动的开销。要运行测试：

``` shell
pytest
```

完成后 - 您可能需要清理 docker 容器：

``` shell
docker-compose down
```

## 使用 *limits* 的项目

- [Flask-Limiter](http://flask-limiter.readthedocs.org){target="_blank"} : Flask 应用程序的速率限制扩展。
- [djlimiter](http://djlimiter.readthedocs.org){target="_blank"} : Django 应用程序的速率限制中间件。
- [sanic-limiter](https://github.com/bohea/sanic-limiter){target="_blank"} : Sanic 应用程序的速率限制中间件。
- [Falcon-Limiter](https://falcon-limiter.readthedocs.org){target="_blank"} : Falcon 应用程序的速率限制扩展。
- [django-ratelimiter](https://andriykohut.github.io/django-ratelimiter/){target="_blank"} : Django 应用程序的速率限制装饰器和中间件。

## 参考资料

- [Redis: 速率限制模式 #2](http://redis.io/commands/INCR){target="_blank"}
- [DomainTools: redis 速率限制器](https://github.com/DomainTools/rate-limit){target="_blank"}
- [Cloudflare: 我们如何构建能够扩展到数百万个域的速率限制](https://blog.cloudflare.com/counting-things-a-lot-of-different-things/){target="_blank"}

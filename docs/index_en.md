---
hide:
  - navigation
---

# limits

![https://github.com/alisaifee/limits](https://img.shields.io/github/last-commit/alisaifee/limits?logo=github&style=for-the-badge&labelColor=#282828)
![https://github.com/alisaifee/limits/actions/workflows/main.yml](https://img.shields.io/github/actions/workflow/status/alisaifee/limits/main.yml?logo=github&style=for-the-badge&labelColor=#282828)
![https://github.com/alisaifee/limits](https://img.shields.io/codecov/c/github/alisaifee/limits?logo=codecov&style=for-the-badge&labelColor=#282828)
![https://pypi.org/project/limits](https://img.shields.io/pypi/v/limits?style=for-the-badge&logo=pypi)

**limits** is a python library for rate limiting via multiple strategies with commonly used storage backends (Redis, Memcached & MongoDB).

The library provides identical APIs for use in sync and `async` codebases.

Get started by taking a look at `Installation` and `Quickstart`.

To learn more about the different strategies refer to the `Rate Limiting Strategies` section.

For an overview of supported backends refer to `Storage Backends`.

## Development

The source is available on [Github](https://github.com/alisaifee/limits).

To get started

``` shell
git clone https://github.com/alisaifee/limits.git
cd limits
pip install -r requirements/dev.txt
```

Since `limits` integrates with various backend storages, local development and running tests
requires a working [docker & docker-compose installation](https://docs.docker.com/compose/gettingstarted/).

Running the tests will start the relevant containers automatically - but will leave them running
so as to not incur the overhead of starting up on each test run. To run the tests:

``` shell
pytest
```

Once you're done - you will probably want to clean up the docker containers:

``` shell
docker-compose down
```

## Projects using *limits*

- [Flask-Limiter](http://flask-limiter.readthedocs.org){target="_blank"} : Rate limiting extension for Flask applications.
- [djlimiter](http://djlimiter.readthedocs.org){target="_blank"} : Rate limiting middleware for Django applications.
- [sanic-limiter](https://github.com/bohea/sanic-limiter){target="_blank"} : Rate limiting middleware for Sanic applications.
- [Falcon-Limiter](https://falcon-limiter.readthedocs.org){target="_blank"} : Rate limiting extension for Falcon applications.
- [django-ratelimiter](https://andriykohut.github.io/django-ratelimiter/){target="_blank"} : Rate limiting decorator and middleware for Django applications.

## References

- [Redis: rate limiting pattern #2](http://redis.io/commands/INCR){target="_blank"}
- [DomainTools: redis rate limiter](https://github.com/DomainTools/rate-limit){target="_blank"}
- [Cloudflare: How we built rate limiting capable of scaling to millions of domains](https://blog.cloudflare.com/counting-things-a-lot-of-different-things/){target="_blank"}

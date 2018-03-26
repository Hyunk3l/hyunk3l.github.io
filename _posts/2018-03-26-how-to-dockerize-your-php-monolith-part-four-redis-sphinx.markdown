---
layout: post
title:  "How to dockerize your old PHP monolith - part four: Redis and Sphinx"
img: posts/how-to-dockerize-php-monolith-part-four-redis-sphinx.png
image: posts/how-to-dockerize-php-monolith-part-four-redis-sphinx.png
categories: [php, docker]
tags: [php, docker, dockerize, monolith, redis, sphinx]
---

Here it is the fourth part of [How to dockerize your old PHP monolith](https://www.fabridinapoli.com/how-to-dockerize-your-php-monolith-part-one/).
Take a look at the previous part [How to dockerize your old PHP monolith - Part Three: php-fpm](https://www.fabridinapoli.com/how-to-dockerize-your-php-monolith-part-three-php-fpm/).
In this post we are going to add Redis and Sphinx containers.

## Redis
Let's start with Redis container.
We are going to use [Redis official image in Docker Hub](https://hub.docker.com/_/redis/); let's choose a concrete version in [tags page](https://hub.docker.com/r/library/redis/tags/).
Hopefully you have a "modern" version, at least 3.2, but if not you have an old 2.8 version (2.8-32bit) available in Docker Hub.
Since most monoliths usually have old Redis versions, we are going to use 2.8.

Create a new `Dockerfile` in `docker/redis` and add this content:
```
FROM redis:2.8-32bit
```

### Redis conf
Now, if you have a personalized redis.conf (Redis configuration file), let's copy it in `docker/redis/conf` directory, or copy the default one from Redis' Github repo: [redis.conf](https://raw.githubusercontent.com/antirez/redis/2.8/redis.conf) example.
Add this two lines to the Dockerfile:
```
COPY conf/redis.conf /usr/local/etc/redis/redis.conf

CMD [ "redis-server", "/usr/local/etc/redis/redis.conf" ]
```
This will build Redis container with your redis.conf.
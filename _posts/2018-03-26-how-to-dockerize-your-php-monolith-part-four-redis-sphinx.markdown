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
I you have a personalized redis.conf (Redis configuration file), let's copy it in `docker/redis/conf` directory, or copy the default one from Redis' Github repo: [redis.conf](https://raw.githubusercontent.com/antirez/redis/2.8/redis.conf) example,
as Redis official repo documentation suggests [here](https://hub.docker.com/_/redis/).

Add this two lines to the Dockerfile:
```
COPY ./docker/redis/conf/redis.conf /usr/local/etc/redis/redis.conf

CMD [ "redis-server", "/usr/local/etc/redis/redis.conf" ]
```
This will build Redis container with your redis.conf.


Here is the final structure of `docker/redis` directory:
```shell
docker/redis
??? Dockerfile
??? conf
    ??? redis.conf
```

### Redis + Docker Compose
Add following lines to the docker-compose.yml file in your project root:
```
  redis:
    build:
      context: ./
      dockerfile: ./docker/redis/Dockerfile
    ports:
      - 6379:6379
```
We are exposing port `6379` that is the default for Redis, but if you are using a personalized one in your `redis.conf`, you can change it.
Really we don't need to expose any port since they are automatically exposed between containers, within the network created by docker-compose, but we are doing it
because you can connect to the Redis container using `redis-cli` without entering the container.
For any doubt, take a look at official [Docker compose network documentation](https://docs.docker.com/compose/networking/).

### Run
Run following command:
```
docker-compose up --build
```
You should see, within other logs:
```
redis_1  | [1] 26 Mar 21:36:52.821 # Server started, Redis version 2.8.23
```

### Test Redis with PHP
Let's do a fast test in `index.php` we created previously in `public` directory.
We are going to need [Composer](https://getcomposer.org/) (hope you have it installed in your project) to add [Predis](https://packagist.org/packages/predis/predis).
Run this command:
```
composer require predis/predis
```

This is going to add this lines:
```
{
    "require": {
        "predis/predis": "^1.1"
    }
}
```
to our composer.json file and download dependencies in `vendor` directory in your project root.
Now, let's do a test in `public/index.php`!
Replace the content with the following lines:
```
<?php

require_once __DIR__ . '/../vendor/autoload.php';

$client = new Predis\Client([
    'scheme' => 'tcp',
    'host'   => 'redis',
    'port'   => 6379,
]);
$client->set('foo', 'bar');
$value = $client->get('foo');

var_dump($value);
```
As you can see we are creating a Predis client, with a concrete host: `redis` that is the name of the running Docker container and the
hostname available within the network.
The script sets a variable in Redis, gets it back and finally print it with a `var_dump`.
If it's all right, if you go to: [http://your-great-monolith.com/](http://your-great-monolith.com/)
you should see this: `string(3) "bar"`
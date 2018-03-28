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
├── Dockerfile
└── conf
    └── redis.conf
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

## Sphinx
Easy enough for the rest of the containers, but Sphinx is an hard-ass.
There is no Docker Hub Sphinx official image but there are some alternatives:
 - Use a "homemade" image, like [this one](https://hub.docker.com/r/centurylink/sphinx/)
 - Build your own image from scratch
 
First option is more comfortable imo, so I'll use it, but depending on your monolith, you could need to build it from scratch.
Let's create a `conf` directory in `docker/sphinx` and add a `sphinx.conf` inside of it.
We will need this file to add our indexes.
Now create a `Dockerfile` in `docker/sphinx` with this content:
```
FROM centurylink/sphinx:2.1.9.02

COPY ./docker/sphinx/conf/sphinx.conf /usr/local/etc/sphinx.conf
```

and add following lines to the `docker-compose.yml` file:
```
  sphinx:
    build:
      context: ./
      dockerfile: ./docker/sphinx/Dockerfile
    ports:
      - 9306:9306
```

Now run `docker-compose up --build`.

You will see something like:
 ```shell
 sphinx_1  | Sphinx 2.1.9-id64-release (rel21-r4761)
 sphinx_1  | Copyright (c) 2001-2014, Andrew Aksyonoff
 sphinx_1  | Copyright (c) 2008-2014, Sphinx Technologies Inc (http://sphinxsearch.com)
 sphinx_1  | 
 sphinx_1  | using config file '/usr/local/etc/sphinx.conf'...
 sphinx_1  | FATAL: no indexes found in config file '/usr/local/etc/sphinx.conf'
 ```
 
Don't worry, it's all right! Since we have no indexes defined in `sphinx.conf`, Sphinx starts up and exits with code 1.
We are going to create indexes in the next part of this tutorial, when we will create a database.

Finally, this should be the structure of `docker/sphinx`:
```shell
docker/sphinx
├── Dockerfile
└── conf
    └── sphinx.conf
```

## Part five
In the fifth and probably the last part, we will build our last container: MariaDb!
Furthermore, we will connect Sphinx container with MariaDb, in order to have data available in our search engine.
Stay tuned!
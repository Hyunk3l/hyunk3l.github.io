---
layout: post
title:  "How to dockerize your old PHP monolith - part two: Nginx"
img: posts/how-to-dockerize-php-monolith-part-two.png
image: posts/how-to-dockerize-php-monolith-part-two.png
categories: [php, docker]
tags: [php, docker, dockerize, monolith, dotenv, symfony, nginx]
---

First post of 2018 and the second part of [How to dockerize your old PHP monolith](https://www.fabridinapoli.com/how-to-dockerize-your-php-monolith-part-one/).
Finally I decided to break down the post in more than two entries. So in this post we are going to see how to dockerize Nginx.
**Remember:** this post does not pretend to be your definitive guide to how to dockerize your old PHP monolith, it’s just my own experience I'm sharing, hoping to do life easier for someone else.

## Overview
We are going to create a basic dockerized version of your monolith, using Docker and [Docker Compose](https://docs.docker.com/compose/overview/).

## Dockerize
 
Let's create a `docker` directory in your project root and, within it, a dir for each container:
```
docker
├── mariadb
├── nginx
├── php
├── redis
└── sphinx
```

Now create a `docker-compose.yml` in your project root dir, with this content:
```
version: '3'

services:
  nginx:
    build:
      context: ./
      dockerfile: ./docker/nginx/Dockerfile
    ports:
      - 80:80
```

### How to choose the right docker image
Alpine linux based images are [probably the best option](https://blog.codeship.com/alpine-based-docker-images-make-difference-real-world-apps/), but not always fit all the needs.
Getting a phrase from the official site: `Alpine makes a great docker container, because it is so small and optimized to be run in RAM`.
There are a lot of alternatives, such as Debian Jessie (Slim) or build your own base image.

### Nginx
First of all, you need to choose the docker image that fits your needs.
Here is the [official Nginx](https://hub.docker.com/_/nginx/) repository in Docker Hub.
Let's choose your Nginx version taking a look at [tags page](https://hub.docker.com/r/library/nginx/tags/).
If you have an old version of Nginx in production, for instance 1.8, and you can't find it in tags page, try to add it in your Dockerfile
and you will get it:
```
Sending build context to Docker daemon  2.048kB
Step 1/1 : FROM nginx:1.8
1.8: Pulling from library/nginx
efd26ecc9548: Pull complete 
a3ed95caeb02: Pull complete 
24941909ea54: Pull complete 
7e605cb95896: Pull complete 
Digest: sha256:c97ee70c4048fe79765f7c2ec0931957c2898f47400128f4f3640d0ae5d60d10
Status: Downloaded newer image for nginx:1.8
 ---> 0d493297b409
Successfully built 0d493297b409
```
This is **NOT RECOMMENDED** because the tag is not in tags list.
Alternatively, you can go to [Nginx Docker Github Repo](https://github.com/nginxinc/docker-nginx), search for the version you need within the "tags"
section and take a look at Dockerfile, in order to take a cue to build your own image. 

So, the version choosed is `1.13-alpine`, let's create a `Dockerfile` within the `docker/nginx/` directory, with this content:
```
FROM nginx:1.13-alpine
```

Give it a try: `docker-compose up --build`, open `http://localhost/` in your browser and you should get the Nginx welcome page.


## Part three
Next part will be about how to create a PHP container and use it with Nginx.


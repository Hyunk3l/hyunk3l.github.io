---
layout: post
title:  "How to dockerize your old PHP monolith - part two: Nginx and PHP"
img: posts/how-to-dockerize-php-monolith-part-two.png
image: posts/how-to-dockerize-php-monolith-part-two.png
categories: [php, docker]
tags: [php, docker, dockerize, monolith, dotenv, symfony, nginx]
---

First post of 2018 and the second part of "How to dockerize your old PHP monolith".
Finally I decided to break down the post in more than two entries. So in this post we are going to see how to dockerize Nginx and PHP.
*Remember:* this post does not pretend to be your definitive guide to how to dockerize your old PHP monolith, it?s just my own experience I'm sharing, hoping to do life easier for someone else.

## Overview
We are going to create a basic dockerized version of your monolith, using Docker and Docker Compose.
Normally

## Dockerize
 
Let's create a `docker` directory in your project root and, within it, a dir for each container:
```
docker
??? mariadb
??? nginx
??? php
??? redis
??? sphinx
``` 

### Nginx
First of all, you need to choose the docker image that fits your needs.
Here is the [official Nginx](https://hub.docker.com/_/nginx/) repository in Docker Hub.
Choose your Nginx version!
Note: 


we are going to create a `Dockerfile` within the `docker/nginx/` directory.



### PHP
Since we're using Nginx, we are 

### MariaDb

### Sphinx

### Redis

## Conclusions

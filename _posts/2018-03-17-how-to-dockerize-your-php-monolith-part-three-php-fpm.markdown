---
layout: post
title:  "How to dockerize your old PHP monolith - part three: PHP-FPM"
img: posts/how-to-dockerize-php-monolith-part-three-php-fpm.png
image: posts/how-to-dockerize-php-monolith-part-three-php-fpm.png
categories: [php, docker]
tags: [php, docker, dockerize, monolith, php-fpm, nginx]
---

Here it is the third part of [How to dockerize your old PHP monolith](https://www.fabridinapoli.com/how-to-dockerize-your-php-monolith-part-one/).
Take a look at the [first post](https://www.fabridinapoli.com/how-to-dockerize-your-php-monolith-part-one/) and the [second one](https://www.fabridinapoli.com/how-to-dockerize-your-php-monolith-part-two-nginx/).
In this post we will create a Dockerfile for [php-fpm](http://php.net/manual/en/install.fpm.php), build it and link to Nginx, using docker-compose.
Let's start.

## PHP-FPM
Remember, from the part two, we created a directories structure?
```
docker
├── mariadb
├── nginx
├── php
├── redis
└── sphinx
```

Now let's create a new Dockerfile in `docker/php/` directory with this content:
```
FROM php:5.6.34-fpm-alpine
```
by the moment, it's all what we need. Why don't we use `image` option in docker-compose?
Because we will need Dockerfile to add site's php custom configurations and packages!

### Why PHP-FPM
It's a good alternative to the normal PHP installation, and there are no much alternatives if you want to use Nginx instead of Apache!
PHP 5.6 is no longer supported (security fixes only) [http://php.net/supported-versions.php](http://php.net/supported-versions.php), but I'm using it because USUALLY PHP monoliths do not use latest PHP versions :)

## Nginx and PHP-FPM
Now it's time to connect Nginx with PHP-FPM.
Create a `conf.d` dir within `docker/nginx`.
This is the directory where we will put our site config.

If you don't have one here is how to create a dummy one:
add a `your-great-monolith-com.conf` with this content
```
server {
    index index.php index.html;
    server_name your-great-monolith.com;
    error_log  /var/log/nginx/your-great-monolith-com-error.log;
    access_log /var/log/nginx/your-great-monolith-com-access.log;
    root /var/www/public;

    location ~ \.php$ {
        try_files $uri =404;
        fastcgi_split_path_info ^(.+\.php)(/.+)$;
        fastcgi_pass php:9000;
        fastcgi_index index.php;
        include fastcgi_params;
        fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
        fastcgi_param PATH_INFO $fastcgi_path_info;
    }
}
``` 
This config is inspired from a `site.conf` from [this post](http://geekyplatypus.com/dockerise-your-php-application-with-nginx-and-php7-fpm/).
Let's see some important stuff from the config:
here is the server_name:
```
server_name your-great-monolith.com;
```
you will need to add this line to your `/etc/hosts` file:
```
127.0.0.1   your-great-monolith.com
```
in order to make this test work.
Error and access logs:
```
error_log  /var/log/nginx/your-great-monolith-com-error.log;
access_log /var/log/nginx/your-great-monolith-com-access.log;
```
it's a good practice to personalize file names or path with the name of your site.
And finally the document root, that it's where Nginx will redirect user requests to:
```
root /var/www/public;
```

## Modify Nginx Dockerfile
We're almost done.
We need to add Nginx configurations within the Docker container, adding the entire `docker/nginx` dir to Nginx container.
The Nginx Dockerfile should now be something like this:
```
FROM nginx:1.13-alpine

ADD ./docker/nginx/ /etc/nginx/
```

## Create public directory
Create a `public` dir in the project root, and create a `index.php` file with this content:
```
<?php
    phpinfo();
```
This directory will be our monolith public dir.

## Add php service to docker-compose
It's time to add php service to docker-compose.yml file and some other stuff.
Here is how `docker-compose.yml` should look like:
```
version: '3'

services:
  nginx:
    build:
      context: ./
      dockerfile: ./docker/nginx/Dockerfile
    ports:
      - 80:80
    volumes:
      - ./:/var/www/
    depends_on:
      - php

  php:
    build:
      context: ./
      dockerfile: ./docker/php/Dockerfile
    volumes:
      - ./:/var/www/
```
As you can see we are now mounting the whole project within the `/var/www` directory, in both php and nginx containers.
Furthermore, we are not saying to Nginx service, to depend on php one.

## Project structure
After all changes done, this should be the current project dir structure:
```
├── docker
│   ├── mariadb
│   ├── nginx
│   │   ├── Dockerfile
│   │   └── conf.d
│   │       └── your-great-monolith-com.conf
│   ├── php
│   │   └── Dockerfile
│   ├── redis
│   └── sphinx
├── docker-compose.yml
└── public
    └── index.php
```

## Build & run with Docker compose
The same as we did in the [previous chapter](https://www.fabridinapoli.com/how-to-dockerize-your-php-monolith-part-two-nginx/), let's build & run containers using Docker compose:
```
docker-compose up --build
```

Open your browser and go to `http://your-great-monolith.com/` and if you have followed all steps correctly, you should see:
![php info for php-5.6 fpm](/images/posts/php-info-5-6.png)


![Homer Simpson woho](https://media.giphy.com/media/xT5LMHxhOfscxPfIfm/giphy.gif)


In future chapters, we will see how to add a personalised `php.ini`, php extensions, xdebug...etc

## Part four
Next part will be about create a Redis container and link it with Nginx and PHP-FPM.


---
layout: post
title:  "How to dockerize your old PHP monolith - part one"
img: posts/how-to-dockerize-php-monolith.png
categories: php, docker
---

Welcome to the first part of **How to dockerize your old PHP monolith**.
This post does not pretend to be your definitive guide to how to dockerize your old PHP monolith, it’s just my own experience I'm sharing hoping to do life easier for someone else.

# What did I have?

* Local environment: a virtual machine with software installed manually.
PHP, MariaDb, Sphinx and Redis. Each dev had it own personalization in virtual machine.

* Production environment: same software as local env, same minor versions, but different bugfixes.
Big differences between envs.

## Application Configurations
You may need to do some changes in code in order to make your monolith run in Docker containers.

First of all changes, is in application configurations. Why? Because you will need to manage environment variables within a few files, instead of searching for them in the whole app.
Is your code prepared to support multiple environments?
If answer is no, here is a possible solution for you!

1. Choose a way to manage your environment variables: [DotEnv](https://symfony.com/doc/current/components/dotenv.html) with plane php or [Symfony Config](https://symfony.com/doc/current/components/config.html).
2. In my case I chose DotEnv. Create a .env.* file for each environment, example: .env.dev, .env.pre, .env.prod
3. Now you will need to copy all those environment-sensitive things like: urls, paths, services host, usernames etc, within the .evn files
   An example:
   ```php
   if ($environment === "dev") {
      $url = "http://service-dev-env-url";
   } else if ($environment === "pre") {
      $url = "http://service-pre-env-url";
   } 
   ```
   you will have in `.env.dev`
   ```php
   SERVICE_URL=http://service-dev-env-url
   ```
   
   and in `.env.pre`
   ```php
   SERVICE_URL=http://service-pre-env-url
   ```
   
4. Start replacing hardcoded stuff or strange if else if blocks with something like:
   
   ```php
   $url = $config->getServiceUrl();
   ```


## Software from Production
Do you have server configurations under version control? Are you using Ansible, Puppet or Chef?
If answer is no, get installed software versions and configurations from production environment.
Imho is the best way to ensure your app will run, within containers, with the same software as in production.

## Part two
Next part will be about how to create docker containers and conclusion.

## Useful links

* [Using DotEnv (.env) in your Symfony project](http://www.oliveradria.com/dotenv-symfony-project/)

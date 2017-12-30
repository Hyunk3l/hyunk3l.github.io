---
layout: post
title:  "Using Postgres with Spring Jdbc Template in a few steps"
img: posts/spring-postgresql.png
categories: java, programming, spring, spring-jdbc, database, postgres
tags: [java, programming, spring, database, sql, postgres, jdbc, docker]
---
In this post I'm going to explain how to use Postgres in your Spring project, using JdbcTemplate, in a few steps and without doing a lot of code.
Spring provides us a lot of utilities, so we are not going to reinvent the wheel each time. Let's use some of them.

## The docker-compose.yml file
We are going to do some test using a Docker container, instead of installing a Postgres directly in our machine.
Here is an example of `docker-compose.yml`:

```
version: "3.1"

services:
  database:
    image: postgres:10
    environment:
      POSTGRES_PASSWORD: example
    volumes:
      - ./docker/postgres/data:/var/lib/postgresql/data
    ports:
      - 5432:5432
```
Note: don't use silly and unencrypted password in production code :D

## The dependencies
We are going to need [Spring Jdbc](https://mvnrepository.com/artifact/org.springframework/spring-jdbc), [Postgres drivers](https://mvnrepository.com/artifact/org.postgresql/postgresql) and [Hikari Connection Pool](https://mvnrepository.com/artifact/com.zaxxer/HikariCP).
Add following dependencies in your `build.gradle` file:
```
...

// Database.
compile group: 'org.springframework', name: 'spring-jdbc', version: '4.3.10.RELEASE'
compile group: 'org.postgresql', name: 'postgresql', version: '42.1.4'
compile group: 'com.zaxxer', name: 'HikariCP', version: '2.6.3'
...
```

## The config

Here is the `application.yml`:

```
spring:
  application:
    name: yourappname
  datasource:
    url: jdbc:postgresql://localhost:5432/name_of_your_database?stringtype=unspecified
    username: postgres
    password: example
```
Note: same as before, do not use silly and unencrypted passwords in prod :D

## The code

Add `JdbcTemplate`, autowiring it, within the configuration, here is an example:
```
...
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.jdbc.core.JdbcTemplate;

@Configuration
public class RepositoryConfiguration {

    @Bean
    public UserRepository userRepository(JdbcTemplate jdbcTemplate) {
        return new PostgresUserRepository(jdbcTemplate);
    }
}
```

Now run your app `./gradlew bootRun` and ...should work!
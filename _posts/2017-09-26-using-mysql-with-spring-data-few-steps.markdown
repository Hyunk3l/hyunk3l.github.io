---
layout: post
title:  "Using MySQL with Spring Data in a few steps"
img: posts/spring-mysql.png
categories: java, programming, spring, spring-data, database, mysql
---
After this big unsuccess of [this post about "Using Postgres with Spring Data in a few steps"](http://www.fabridinapoli.com/using-postgres-with-spring-data-few-steps/) I'm going to make another post but this time using MySQL.
Let's start!

## The docker-compose.yml file
We are going to do some test using a Docker container, instead of installing a MySQL directly in our machine.
Here is the `docker-compose.yml` I'm using:
```
version: '3.1'

services:
  database:
    image: mysql
    environment:
      MYSQL_ROOT_PASSWORD: example
    volumes:
      - ./docker/mysql/data:/var/lib/mysql/data
    ports:
      - 3306:3306
```
got from [MySQL's Docker hub page](https://hub.docker.com/_/mysql/).
Note: as always, don't use silly and unencrypted password in production code :D

## The dependencies
We are going to need [Spring Jdbc](https://mvnrepository.com/artifact/org.springframework/spring-jdbc) and [MySQL java connector](https://mvnrepository.com/artifact/mysql/mysql-connector-java).
Just add following lines in your `build.gradle` file, in dependencies object (with other dependencies):
```

dependencies {
...
compile("org.springframework.boot:spring-boot-starter-jdbc")
compile group: 'mysql', name: 'mysql-connector-java', version: '6.0.6'
...
}
```

## The config
Here is the `application.yml`:

```
spring:
  application:
    name: yourappname
  datasource:
    url: jdbc:mysql://127.0.0.1:3306/mysql
    username: root
    password: example
```

## The code

Add `JdbcTemplate` within the configuration, here is an example:
```
...
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.jdbc.core.JdbcTemplate;

@Configuration
public class RepositoryConfiguration {

    @Bean
    public UserRepository userRepository(JdbcTemplate jdbcTemplate) {
        return new MySqlUserRepository(jdbcTemplate);
    }
}
```

Now run your app `./gradlew bootRun`, should work!
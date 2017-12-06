---
layout: post
title:  "NamedParametersJdbcTemplate: JdbcTemplate prepared statement with named params"
img: posts/spring-namedparameterjdbctemplate.png
categories: java, jdbctemplate, database
---

When you start with Spring JdbcTemplate, and need to pass parameters to the prepared statement,
you normally use question marks.

## Example

Here is the query:
```$sql
SELECT
  id,
  name,
  surname
FROM
  users
WHERE
  id = ?
;
```

and here is the code to run it:
```$java
String query = "...";
Integer userId = 1;

jdbcTemplate.query(
  query,
  new RowMapper<T>() {...},
  userId
);
```
automatically JdbcTemplate will replace question marks with values in method signature, starting from the third one.
Here is another example, with multiple values (silly query):
```
SELECT
  id,
  name,
  surname
FROM
  users
WHERE
  id = ?
  OR id = ?
;
```
and here it is the java code:
```$java
String query = "...";
Integer userId = 1;
Integer anotherUserId = 2;

jdbcTemplate.query(
  query,
  new RowMapper<T>() {...},
  userId,
  anotherUserId
);
```

Now imagine you have a query with repeated parameters, such as an UNION query, to search for an user within your entire system (active users or archived one):
```$sql
(
  SELECT
    id,
    name,
    surname
  FROM
    users
  WHERE
    id = ?
    OR id = ?
)
UNION
(
  SELECT
    id,
    name,
    surname
  FROM
    deleted_users
  WHERE
    id = ?
    OR id = ?
);
```
you can pass same values to query method multiple times or use *NamedParameterJdbcTemplate*.

## NamedParameterJdbcTemplate: an example

First of all you will need to use NamedParameterJdbcTemplate in the repository you are going to declare in Spring configurations:
```$java
@Bean
public UserRepository userRepository(NamedParameterJdbcTemplate jdbcTemplate) {
  return new MySqlUserRepository(jdbcTemplate);
}
```

Same example as before, but with named parameters instead of question marks:
```$sql
(
  SELECT
    id,
    name,
    surname
  FROM
    users
  WHERE
    id = :userId
    OR id = :anotherUserId
)
UNION
(
  SELECT
    id,
    name,
    surname
  FROM
    deleted_users
  WHERE
    id = :userId
    OR id = :anotherUserId
);
```

and here it is the java code:
```$java
String query = "...";

return jdbcTemplate.query(
  query,
  new MapSqlParameterSource("userId", 1)
    .addValue("anotherUserId", 2),
  new RowMapper<T>() {...},
);
```

That's all!
Hope you enjoyed the post!
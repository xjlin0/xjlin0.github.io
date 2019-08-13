---
layout: post
title: "Running native queries in ORM"
description: "Sometimes the ORM mathods is just not enough"
category: tech
tags: [SQL, Ruby, Python, Java, Database, ActiveRecord, Rails, Django, Spring, JPA]
---
{% include JB/setup %}
### Sometimes the ORM mathods is just not enough. <img src="/assets/imgs/db_queries.jpg"  alt="finding out who knows the top secret" width="20%"/>

Object-Relational Mapping (ORM) is great! There are some many methods immediately available without crafting SQL, we can often even get the results without crazy join.  Filters, aggregations..., etc. you name it!

However, no tools are 100% perfect, sometimes, we just have to join 10 tables to get the desired results.  But how to run native SQL queries in ORM?  The followings are two ways I like in Rails.  Sometimes the results of the raw SQL needed to be map to existing classes.  Sometimes it is enough to get array of hashes.  It is elegant to use `ActiveRecord::Result`'s `columns` when the attributes are needed.

```ruby
# Map results to instances of an existing class
supermen = User.find_by_sql('SELECT * FROM users WHERE super_power = 1')

# Map results to ActiveRecord::Result
users = ActiveRecord::Base.connection_pool.with_connection do |connection|
	connection.exec_query('SELECT * FROM users') # Map results to ActiveRecord::Result
	#connection.select_all('SELECT * FROM users') # Map results to Array of Hashes
end
user_count = users.count
last_user = User.instantiate(users.last)
```
But what if you are using JPA?  If no proper named queries can be found, can we also run native queries?  You bet!

```java
package com.supermen.incredibles.repository;


import com.supermen.incredibles.model.UserEntity;
import org.springframework.data.jpa.repository.JpaRepository;
import org.springframework.data.jpa.repository.Query;
import org.springframework.data.repository.query.Param;

import javax.inject.Named;
import java.util.List;


@Named
public interface UserRepository extends JpaRepository<UserEntity, String> {

   @Query( // and join other 10 tables if needed
      value = "SELECT * FROM users WHERE name = :name",
      nativeQuery = true
   )
   List<UserEntity> findUsersByRawSql(@Param("name") String name);
}
```

OK, if using Python/Django, could I also run native queries?  Here is how:

```python
# map the results to queryset of Model
user = User.objects.raw('SELECT * FROM users WHERE name = %s', ["jack"])
# please pass parameters as a list to avoid SQL injection attacks.

# direct running queries
from django.db import connection
from collections import namedtuple

# use it like a list of tuples like some_users[0]
with connection.cursor() as cursor:
	cursor.execute("SELECT * FROM users WHERE name = %s", ["Bob"])
	some_users = cursor.fetchall()

# use it like a list of objects like other_users[0].name
with connection.cursor() as cursor:
	cursor.execute("SELECT * FROM users WHERE name = %s", ["Parr"])
	nt_result = namedtuple('Result', [col[0] for col in cursor.description])
	other_users = [nt_result(*row) for row in cursor.fetchall()]
```

That's it. Enjoy!

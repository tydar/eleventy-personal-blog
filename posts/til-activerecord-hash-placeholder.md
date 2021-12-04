---
title: "TIL: ActiveRecord hash conditions and parameterized conditions"
date: 2021-12-03
tags:
    - til
    - ruby
    - rails
layout: layouts/post.njk
---
ActiveRecord allows the use of 'hash conditions' to construct `WHERE` clauses like so:

```ruby
Person.where(name: "Tyler")
```

You can also use hash conditions when joining:

```ruby
Person.joins(:orders).where(orders: { pending: true })
```

But you cannot use hash conditions to create queries based on SQL keywords like `LIKE`. Instead you must use
string conditions and placeholders like this Postgres example:

```ruby
Person.where("email LIKE ?", "%gmail.com")
```

Parameterized queries like this allow SQL injection mitigations to be applied by ActiveRecord where

```ruby
Person.where("email LIKE %#{INPUT}")
```

would be unsafe!

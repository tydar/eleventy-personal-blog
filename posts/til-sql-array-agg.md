---
title: "TIL: array_agg SQL aggregate function"
date: 2022-01-07
tags:
  - til
  - sql
  - postgres
layout: layouts/post.njk
---

Recently working on a reporting query in [Stripe Sigma](https://stripe.com/sigma) I needed to aggregate some `VARCHAR` type fields.

I learned about the aggregate function `ARRAY_AGG`, which turns a set of values into an array of values. It is supported by Postgres and by [Trino](https://trino.io/), the query engine used by Stripe Sigma.

Here's an example table:

| id | fk_id | content |
------------------------
| 1  | 100 | row 1     |
| 2  | 100 | row 2     |
| 3  | 200 | row 3     |

And a query:

```sql
select
   fk_id,
   array_agg(content) as content_array
from
   table
group by fk_id
```

And a result:

| fk_id | content_array      |
-----------------------------
| 100   | ["row 1", "row 2"] |
| 200   | ["row 3"]          |

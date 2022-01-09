---
title: "TIL: Working with nullable rows and the Go driver pgx"
date: 2022-01-09
tags:
  - til
  - golang
  - postgres
layout: layouts/post.njk
---

On a small Go webapp project I am developing, I have a table in a Postgres db defined this way:

```sql
CREATE TABLE recipes (
	id serial primary key,
	title text not null,
	book text not null,
	page_num integer not null,
	notes text,
	last_made date
);
```

I am using the DB driver [pgx](https://pkg.go.dev/github.com/jackc/pgx) to develop this app. Since `notes` and `last_made` are nullabe but the primitive Go types they correspond to are not, the library [pgtype](https://pkg.go.dev/github.com/jackc/pgtype) provides a compatible interface to work with these types neatly.

For example, `notes` can be scanned into a value of type `pgtype.Text`. `pgtype.Text` has a member value of type `pgtype.Status`, which is an enumeration with values `pgtype.Undefined`, `pgtype.Null`, and `pgtype.Present`. So I can neatly handle any value like so:

```go
var notesPg pgtype.Text

row.Scan(..., &notesPg, ...)

notes := ""
if notesPg.Status == pgtype.Present {
	if err := notesPg.Scan(notes); err != nil {
		// deal with it
	}
}
```

And have either an empty string or the value from the db at the end, which works just fine for my purpose!

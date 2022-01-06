---
title: "TIL: MongoDB array update with $push and $pull"
date: 2022-01-06
tags:
  - til
  - mongo
  - golang

layout: layouts/post.njk
---

In [mdbssg](https://github.com/tydar/mdbssg) my small static site generator project I started to continue sharpening Go skills and begin learning MongoDB basics for my current job, I keep user sessions in an array within the user document, like so:

```mongodb
{
	"_id": ObjectID("..."),
	"username"": "...",
	"sessions": [
		"token": "...",
		"expires_at": "...",
	],
}
```

Without looking into the db, I was writing update logic in Go like this snippet, to add a new session:

```go
// error handling elided for space
sessions, err := u.getSessionsByUsername(ctx, username)

session := Session{
	Token:     uuid.NewString(),
	ExpiresAt: time.Now().Add(5 * time.Minute),
}
sessions = append(sessions, session)

users := u.client.Database(u.dbName).Collection("users")
_, err = users.UpdateOne(ctx, bson.M{"username": username}, bson.M{"$set": bson.M{"sessions": sessions}})
```

I would build a new slice in Go and `$set` the `sessions` array for the given `user` document in Mongo.

I learned later that I could instead rewrite similar logic with `$push` to add new array entries and `$pull` to delete entries, like this logic to remove all expired sessions for a given user:

```go
// given a username, remove expired sessions with an updateMany(...{ '$pull': ... })
func (u *UserModel) removeExpiredSessions(ctx context.Context, username string) (int, error) {
	sessions, err := u.getSessionsByUsername(ctx, username)
	if err != nil {
		return 0, err
	}

	sessTokens := make([]string, 0)
	for i := range sessions {
		if sessions[i].ExpiresAt.Before(time.Now()) {
			sessTokens = append(sessTokens, sessions[i].Token)
		}
	}

	users := u.client.Database(u.dbName).Collection("users")

	// note: db.collection.updateMany() is not atomic. I think this could cause issues if a user is trying
	// to sign in at one location (so this method is called) while that account is in use at another location
	mr, err := users.UpdateMany(ctx, bson.M{},
		bson.M{"$pull": bson.M{"sessions": bson.M{"token": bson.M{"$in": sessTokens}}}})
	return int(mr.ModifiedCount), err
}
```

which produces a query like:

```mongodb
db.users.updateMany(
	{
		username: "..."
	},
	{
		$pull: { sessions: { token: { $in: [...] } } }
	}
)
```

I'm not sure which is more efficient to use--but 

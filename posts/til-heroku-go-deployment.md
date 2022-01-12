---
title: "TIL: Deploying Go apps on Heroku"
date: 2022-01-11
tags:
  - til
  - golang
  - heroku
  - devops
layout: layouts/post.njk
---

I recently deployed my projects [recipe randomizer](https://github.com/tydar/reciperandomizer) and [mdbssg](https://github.com/tydar/mdbssg) to Heroku. I had never deployed anything to Heroku before (having only used cloud offerings like EC2 and Digital Ocean droplets in the past), so I learned a few things.

1) Heroku's [Go buildpack](https://github.com/heroku/heroku-buildpack-go) supports a lot of different Go projects. 
2) Heroku also supports [Docker builds](https://devcenter.heroku.com/articles/build-docker-images-heroku-yml) on even free-tier projects now.

Since I was already familiar with building Go apps in Docker, I decided to take that route. That means writing a Dockerfile like this:

```Dockerfile
##
## BUILD
## 
FROM golang:1.17-bullseye AS build

WORKDIR /app

COPY go.mod ./
COPY go.sum ./

RUN go mod download
COPY *.go ./
COPY models/*.go ./models/
COPY handlers/*.go ./handlers/
COPY templates/*.html ./templates/

RUN go build -o /reciperandomizer

##
## Deploy
##
FROM gcr.io/distroless/base-debian10 AS deploy

WORKDIR /app

COPY --from=build /reciperandomizer ./reciperandomizer
COPY templates/*.html ./templates/

USER nonroot:nonroot

ENTRYPOINT [ "/app/reciperandomizer" ]
```

Using a multi-stage build as advised by [the Docker docs for Go](https://docs.docker.com/language/golang/build-images/).

This also requires a `heroku.yml` file that tells Heroku how to deploy any container(s) your app needs:

```yml
build:
  docker:
    web: Dockerfile
run:
  web: ./app/reciperandomizer
```

Now you can create a Heroku app for your project and set the heroku build stack to container.

```shell-session
$ heroku app:create
$ heroku stack:set container
$ git push heroku master
```

And Heroku will build your Docker container and run your app as instructed in the `heroku.yml` file. Note there is no `EXPOSE` directive in the Dockerfile. This is because Heroku will assign a port to your app just like when deploying a non-dockerized app on heroku.

---
title: "TIL: Configure Nginx as a load balancer"
date: 2021-01-15
tags:
    - nginx
    - devops
    - til
layout: layouts/post.njk
---
Configuring Nginx to act as a load balancer for one service is done easily with a simple configuration file.

``` nginx
http {
    upstream service1 {
        server n1.service1.example.com;
        server n2.service1.example.com;
    }   
}

server {
    location / {
        proxy_pass http://service1
    }
}
```

By default, Nginx uses a round-robin load-balancing method. This means each server gets an even distribution of load. Weights are taken in account.

Two common alternatives are a least connections and an IP Hash strategy. Least connections is activated by adding a directive to the upstream block:

``` nginx
upstream service1 {
    least_conn;
    # servers go here
}
```

This means the server with the least number of live connections is prioritized. Weights are again considered. IP Hash is activated similarly:

``` nginx
upstream service1 {
    ip_hash;
    # servers go here
}
```

This method ensures requests from a given IP address get a consistent server, unless that server is down. This is important for ensuring consistency with some stateful services.

Additional documentation here: [Nginx: HTTP Load Balancing](https://docs.nginx.com/nginx/admin-guide/load-balancer/http-load-balancer/).

---
title: Configure NGINX as reverse proxy
date: 2021-01-06
tags:
    - devops
    - nginx
layout: layouts/post.njk
---
Basic configuration for NGINX as a reverse proxy is very simple. To pass URLs like `http://example.com/service` to that service on a LAN, you just need to add this entry to an appropriate configuration file within a `server` directive listening on the appropriate port. To proxy typical HTTP requests, that would be port 80.

```
server {
    listen 80;
    location /service/ {
        proxy_pass http://192.168.1.55:8080; 
    }
}
```

So when a request comes to the proxy server for a URL starting with `/service`, the server running on the machine with IP 192.168.1.55 listening at port 8080 receives the request. To proxy requests for multiple different endpoints, you can add additional `location` directives to your configuration file.

In my home 'production' environment I hav set up NGINX to proxy requests to my local DNS admin panel, to proxy requests to my CheckMK instance, and to my personal wiki for managing configuration and how-to notes.

More details from NGINX documentation here: [NGINX Reverse Proxy](https://docs.nginx.com/nginx/admin-guide/web-server/reverse-proxy/).

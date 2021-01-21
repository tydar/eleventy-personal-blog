---
title: "TIL: Using NGINX as a content cache"
date: 2021-01-21
tags:
    - til
    - nginx
layout: layouts/post.njk
---
Configuring an NGINX reverse proxy or load balancer to cache requests is straightforward. To enable a cache, you simply use the `proxy_cache_path` directive in the `http {}` block. There are two required parameters: the local path to store cached content and the `keys_zone` parameter that sets up storage for cache metadata in RAM. In context:

``` nginx
http {
    proxy_cache_path /data/nginx/cache keys_zone=one:10m;
}
```

This configuration line borrowed from [the official NGINX documentation](https://docs.nginx.com/nginx/admin-guide/content-cache/content-caching/) sets up a cache at the path `/data/nginx/cache` with a key zone 10 megabytes in size named 'one'.

Then, to cache requests, include the `proxy_cache` directive on a per server, per protocol, or per location basis:

``` nginx
http {
    proxy_cache_path /data/nginx/cache keys_zone=one:10m;
    server {
        location / {
            proxy_cache rootcache;
            proxy_pass http://root.server.tld:8080; 
        }
    }
}
```

With default settings, all GET and HEAD requests are cached. Some directives exist to limit which requests are cached, as well as to expand the HTTP request types cached. [More documentation here](https://docs.nginx.com/nginx/admin-guide/content-cache/content-caching/), as referenced above.

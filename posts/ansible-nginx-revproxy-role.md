---
title: Configure an Nginx reverse proxy with Ansible
date: 2021-01-16
tags:
    - nginx
    - devops
    - ansible
layout: layouts/post.njk
---
## Objective

Develop a minimal Ansible role that will configure an existing Linux server to run an Nginx reverse proxy. Parameterize the Nginx configuration file based on variables passed to the role. GitHub repo here: [Nginx Ansible Role](https://github.com/tydar/nginx-ansible-role).

## Short recap on role structure

The folder structure for this role is

``` text
roles/
    nginx_reverse_proxy/
        tasks/
            main.yml
        defaults/
            main.yml
        templates/
            nginx.conf.j2
```

In `tasks/main.yml` we place the top-level tasks that will be run when the role is included in a playbook. In `defaults`, we include any default variable definitions in `main.yml`. Placing templates in `templates/` allows unprefixed access by certain Ansible tasks.

## The nginx.conf template

To create an Nginx configuration file using our Ansible variables, we'll take advantage of Ansible's builtin support for Jinja2 templates. Control expressions allow us to iteratively construct `location` blocks for each proxied path.

``` jinja2
{% raw %}
server {
    listen {{ listen_port  }};
    server_name {{ server_name }};
{% if root_pass %}
    location / {
        proxy_pass {{ root_pass_location }};
    }
{% endif %}
{% for location in locations  %}
    location {{ location['path'] }} {
        proxy_pass {{ location['pass_to'] }};
    }
{% endfor %}
}
{% endraw %}
```

For the minimal configuration details for a reverse proxy, we'll need to pass in a list of dictionaries called `locations`, where each dictionary entry has keys named `path` and `pass_to`. We also need to provide the listening port as well as a flag & variable to indicate whether requests for the root URI should be proxied.

This does not make use of Nginx's full location directive features (like changing the URL matching based on case). It also does not provide the ability to set other proxy properties, though that is easily implementable.

## Tasks

The minimal list of tasks for this role are to install Nginx using the correct package manager and to create the appropriate configuration from the template.

## Default vars

By default, I chose to set `listen_port` to 80, `root_pass` to `false`, and `server_name` to "proxy". No other variables in this minimal configuration have a sensible default value.

## Usage

Create a playbook that looks something like this:

``` yml
---
# test_playbook.yml next to roles folder
- name: Set up Nginx reverse proxy on one server
  hosts: revprox
  vars_files:
    - {{ playbook_dir }}/secrets/config.yml
  roles:
    - nginx_reverse_proxy
```

With a file stored next to the playbook at `secrets/config.yml`

``` yml
---
# secrets/config.yml
server_name: proxy
locations:
    - path: /service1/
      pass_to: http://service1.test.tld:8080
    - path: /service2/
      pass_to: http://service2.test.tld:8080
```

And finally an inventory that looks like:

``` yml
---
# inventory/test.yml
revprox:
    server.to.configure.tld:
```

To execute, run the command `ansible-playbook -i inventory/test.yml test_playbook.yml`. This will install Nginx on the remote server. The `default.conf` templated to Nginx will look like this:

``` nginx
# default.conf
server {
    listen 80;
    server_name proxy;
    location /service1/ {
        proxy_pass http://service1.test.tld:8080/
    }
    location /service2/ {
        proxy_pass http://service2.test.tld:8080/
    }
}
```

## Next steps

To improve on this minimal role, I will be adding the ability to specify additional configuration for each proxy pass, as well as configuration for Nginx settings overall (like number of workers, connections per worker, etc). 

Eventually I will refactor this role to allow configuring both standard reverse proxies and load balanced proxies. I'd also like to be able to configure passing requests to memcached, fastcgi, and other protocols.

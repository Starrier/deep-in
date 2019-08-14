# Starrier

[![Build Status](https://travis-ci.org/Starrier/Starrier.svg?branch=master)](https://travis-ci.org/Starrier/Starrier)

[www.starrier.org](http://www.starrier.org)

----

## Blog build with Node.js (Express)

There are two versions of this blog. One of them is the Node.js version, and the other one is Spring Boot wih Spring Cloud version.

> The version of Node is in the branch named node, and the version tag is suffixed with node.

and

> The version of SpringBoot is in the branch named node,and the version tag is suffixed with spring.

## Developer

**participant**:

[Starrier](https://github.com/Starriers)

And if you wants to test the version of the Node, please follow the steps below:

### first

> npm install

### second

> npm start

### thrid

> localhost:3000

## Nginx Reverse Proxy

I use **Nginx** as the reverse proxy server,and here are some of the configurations I used when testing locally.

``` ngnix
 upstream Starrier{
        server 127.0.0.1:3000;
        keepalive 64;
    }

    server {
        listen       80 default_server;
        listen       [::]:80 default_server;
        server_name  127.0.0.1:3000;

        #charset koi8-r;

        #access_log  logs/host.access.log  main;

        location / {
           proxy_pass http://Starrier;
           proxy_http_version 1.1;
        }
```

# NGINX Demo Documentation

This repository contains a demo example of how to set up and configure NGINX on macOS.

## Installation Instructions

### Install NGINX on macOS
To install NGINX on macOS, use Homebrew:
```bash
brew install nginx
```

### Setup `launchd` to Start NGINX
To start NGINX automatically using `launchd`, run:
```bash
brew services start nginx
```

### Check the NGINX Path
To find the path where NGINX is installed, use:
```bash
which nginx
```
Example Output: `/opt/homebrew/bin/nginx`

Alternatively, you can check the version and configuration options with:
```bash
nginx -V
```

## Modify the NGINX Configuration

### Path to the NGINX Config File
The main NGINX configuration file is `nginx.conf`. Below is an example configuration:

```nginx
worker_processes  1;

events {
    worker_connections 1024;
}

http {
    server {
        listen 8080;
        server_name localhost;

        location / {
            proxy_pass http://YOUR_DOMAIN:YOUR_PORT;
        }
    }
}
```

### Example of a TCP Proxy for SQL Server

Below is an example configuration for setting up a TCP proxy with NGINX for SQL Server.

```nginx
#user  nobody;
worker_processes  1;

#error_log  logs/error.log;
#error_log  logs/error.log  notice;
#error_log  logs/error.log  info;

#pid        logs/nginx.pid;

events {
    worker_connections  1024;
}

http {
    include       mime.types;
    default_type  application/octet-stream;

    sendfile        on;
    keepalive_timeout  65;

    server {
        listen       8080;
        server_name  localhost;

        location / {
            root   html;
            index  index.html index.htm;
        }

        error_page   500 502 503 504  /50x.html;
        location = /50x.html {
            root   html;
        }
    }
    
    include servers/*;
}

stream {
    upstream sqlserverropa {
        least_conn;
        server localhost:1434;
    }
    upstream sqlservermuebles {
        least_conn;
        server localhost:1433;
    }

    server {
        listen 1434; 
        proxy_pass sqlserverropa;
        proxy_timeout 30s;
        proxy_connect_timeout 30s;
    }
    server {
        listen 1433; 
        proxy_pass sqlservermuebles;
        proxy_timeout 30s;
        proxy_connect_timeout 30s;
    }
}
```

## Additional Configuration Notes
- Ensure the `nginx.conf` file is correctly placed and configured.
- The `stream` block is used for TCP/UDP proxying, which is ideal for databases like SQL Server.
- Adjust the upstream server addresses and ports according to your setup.

For more detailed information, refer to the [NGINX documentation](https://nginx.org/en/docs/).


### Demo Example PATH: 

For Macos:
https://medium.com/@giorgos.dyrrahitis/configuring-an-nginx-tcp-proxy-for-my-rabbitmq-cluster-under-10-minutes-a0731ec6bfaf

For Linux: 
https://www.digitalocean.com/community/tutorials/how-to-configure-nginx-as-a-reverse-proxy-on-ubuntu-22-04

### Install NGINX Macos
```bash
brew install nginx
```
### Setup launchd to start nginx
```bash
brew services start nginx
```
### Check the NGINX path
``` bash
which nginx
```
Output Example: /opt/homebrew/bin/nginx
or
``` bash
nginx -V
```

#### Modify the NGINX Configuration
##### path to the nginx config file: nginx.conf
```
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
### Example of TCP Proxy for SQL Server
```

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

    #log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
    #                  '$status $body_bytes_sent "$http_referer" '
    #                  '"$http_user_agent" "$http_x_forwarded_for"';

    #access_log  logs/access.log  main;

    sendfile        on;
    #tcp_nopush     on;

    #keepalive_timeout  0;
    keepalive_timeout  65;

    #gzip  on;

    server {
        listen       8080;
        server_name  localhost;

        #charset koi8-r;

        #access_log  logs/host.access.log  main;

        location / {
            root   html;
            index  index.html index.htm;
        }

        #error_page  404              /404.html;

        # redirect server error pages to the static page /50x.html
        #
        error_page   500 502 503 504  /50x.html;
        location = /50x.html {
            root   html;
        }

        # proxy the PHP scripts to Apache listening on 127.0.0.1:80
        #
        #location ~ \.php$ {
        #    proxy_pass   http://127.0.0.1;
        #}

        # pass the PHP scripts to FastCGI server listening on 127.0.0.1:9000
        #
        #location ~ \.php$ {
        #    root           html;
        #    fastcgi_pass   127.0.0.1:9000;
        #    fastcgi_index  index.php;
        #    fastcgi_param  SCRIPT_FILENAME  /scripts$fastcgi_script_name;
        #    include        fastcgi_params;
        #}

        # deny access to .htaccess files, if Apache's document root
        # concurs with nginx's one
        #
        #location ~ /\.ht {
        #    deny  all;
        #}
    }


    # another virtual host using mix of IP-, name-, and port-based configuration
    #
    #server {
    #    listen       8000;
    #    listen       somename:8080;
    #    server_name  somename  alias  another.alias;

    #    location / {
    #        root   html;
    #        index  index.html index.htm;
    #    }
    #}


    # HTTPS server
    #
    #server {
    #    listen       443 ssl;
    #    server_name  localhost;

    #    ssl_certificate      cert.pem;
    #    ssl_certificate_key  cert.key;

    #    ssl_session_cache    shared:SSL:1m;
    #    ssl_session_timeout  5m;

    #    ssl_ciphers  HIGH:!aNULL:!MD5;
    #    ssl_prefer_server_ciphers  on;

    #    location / {
    #        root   html;
    #        index  index.html index.htm;
    #    }
    #}
    include servers/*;
}



stream {
        # SQL Server Ropa
        upstream sqlserverropa {
                least_conn;
                server localhost:1434;
                
        }
        # SQL Server Muebles: 1433
        upstream sqlservermuebles {
                least_conn;
                server localhost:1433;
                
        }

server {
                listen 1434; # the port to listen on this server
                proxy_pass sqlserverropa; # forward traffic to this upstream group
                proxy_timeout 30s;
                proxy_connect_timeout 30s;
        }
server {
                listen 1433; # the port to listen on this server
                proxy_pass sqlservermuebles; # forward traffic to this upstream group
                proxy_timeout 30s;
                proxy_connect_timeout 30s;
        }
}


```

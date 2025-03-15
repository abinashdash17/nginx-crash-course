nginx
# keywords
directives -> key value
context
directive inside context
## contexts
 http,event
 http -> server -> location
 redir,rewrite

proxy_pass uses roundrobin

# useful commands
## pull docker image
`docker pull nginx`
## spin up nginx container
`docker run -d --rm -p 8080-8085:8080-8085 -v $(pwd)/nginx-config/nginx.conf:/etc/nginx/nginx.conf -v $(pwd)/www:/srv/www --name my-nginx nginx`
## attach to the container
`docker exec -it my-nginx bash`
## stop container
`docker stop my-nginx`
## remove container
`docker rm my-nginx`
## reload nginx config
`nginx -s reload`

# config
```
http{
    include mime.types;

    upstream backendserver {
        server 127.0.0.1:8081;
        server 127.0.0.1:8082;
        server 127.0.0.1:8083;
    }
    server {
        listen 8081;
        root /srv/www/test1;
    }
    server {
        listen 8082;
        root /srv/www/test2;
    }
    server {
        listen 8083;
        root /srv/www/test3;
    }
    server{
        listen 8080;
        root /srv/www;

        location /test8 {
            proxy_pass http://backendserver/;
        }
        location /test{
            root /srv/www;
        }
        location /test2{
            alias /srv/www/test;
        }
        location ~* /count/[0-5] {
            root /srv/www;
            try_files /index.html =404;
        }
        location /redir-test{
            return 307 /test;
        }
        rewrite /test5 /test/;
    }

}
events{}

```

# notes
1. /srv/www - contains some dummy html files
2. the dirs are mounted. so edit the file in host system itself.
3. `-s`  is signal . 4 signals are possible.

# References
1. https://www.youtube.com/watch?v=9t9Mp0BGnyI
2. https://github.com/abinashdash17/nginx-crash-course

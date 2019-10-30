

## Sample Nginx Conf file

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
        listen <yourServers'ExposedPort> ssl;
        
        ssl_certificate      <!-- PEMFILECERTIFICATE -->;
        ssl_certificate_key  <!-- KEYFILESECRET -->;
        
        ssl_session_cache    shared:SSL:20m;
        ssl_session_timeout  10m;

        ssl_prefer_server_ciphers  on;
        ssl_protocols              TLSv1.2;
        ssl_ciphers                HIGH:!aNULL:!MD5;

        add_header Strict-Transport-Security "max-age=31536000";

        location / {
            proxy_set_header Host $http_host;
            proxy_set_header X-Real-IP $remote_addr;
            proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
            proxy_set_header X-Forwarded-Proto $scheme;
            proxy_pass http://yourCustomUpstreamName;
        }
    }

    upstream yourCustomUpstreamName{
        ip_hash;
        server <server1.domain>:<server1port>; 
        server <server2.domain>:<server2port>; 
        server <server3.domain>:<server3port>; 
    }

}
```


# Mercure Hub

**Supervisor config:**
`/etc/supervisor/conf.d/mercure_prod.conf`
```
[program:mercure_prod]
command=/opt/mercure/mercure --jwt-key='pwp_backend_scrt' --addr=':3000' --allow-anonymous --cors-allowed-origins='*' --use-forwarded-headers=1 --transport-url='bolt://mercure_updates_prod_1.db?size=10000&cleanup_frequency=0.2'
process_name=%(program_name)s_%(process_num)s
numprocs=1
directory=/opt/mercure
autostart=true
autorestart=true
startsecs=5
startretries=10
user=www-data
redirect_stderr=false
stdout_capture_maxbytes=1MB
stderr_capture_maxbytes=1MB
stdout_logfile=/var/log/mercure_prod.out.log
stderr_logfile=/var/log/mercure_prod.error.log
```

**Nginx config:**
```
server {
    server_name  sse.app.com;
    
    listen 443 ssl http2;
    listen [::]:443 ssl http2;

    ssl_certificate /etc/nginx/ssl/dev.app.com/app_com.crt;
    ssl_certificate_key /etc/nginx/ssl/dev.app.com/app_com.key;

    location / {
       return 404;
    }

    location ~* ^/\.well-known/mercure$ {    
        if ($arg_topic = "*") {
            return 404;
        }

        proxy_pass http://localhost:3000;
        proxy_read_timeout 1000;
        proxy_connect_timeout 1000;
        proxy_send_timeout 1000;
        send_timeout 1000;
        keepalive_timeout 1000;

        proxy_http_version 1.1;
        proxy_set_header Connection "";
        proxy_buffering off;
        proxy_cache off;
        chunked_transfer_encoding off;

        ## Be sure to set USE_FORWARDED_HEADERS=1 to allow the hub to use those headers ##
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Host $host;
        proxy_set_header X-Forwarded-Proto $scheme;
    }
}
```

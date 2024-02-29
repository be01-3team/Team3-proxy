## v1

```
upstream app_server {
    server 192.168.0.49:8080;
}

server {
    listen 80;

    location / {
        proxy_pass http://app_server;
        proxy_set_header Host $host;
    }
}
```

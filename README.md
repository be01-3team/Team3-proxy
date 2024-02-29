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

## v2
```
# /etc/nginx/conf.d/default.conf

proxy_cache_path /var/cache/default levels=1:2 keys_zone=mycache:10m max_size=10g inactive=5s use_temp_path=off;

upstream app_server {
    server 192.168.0.49:8080;
}

server {
    listen 80;

    location / {
        proxy_pass http://app_server;
        proxy_set_header Host $host;
        proxy_cache_bypass $http_upgrade;
        proxy_set_header Connection 'upgrade';
        proxy_set_header Upgrade $http_upgrade;
        proxy_cache_methods GET HEAD POST;
        proxy_cache_valid 200 301 302 304 5s;
        add_header X-Cache-Status $upstream_cache_status;
        add_header Cache-Control "public";
    }
}
```

## 캐시 설정 설명
1. proxy_cache_path
캐시의 경로와 매개변수를 설정합니다. 해당 경로에 디렉토리가 존재하여야하며, path 뒤에 있는 내용들로 설정을 진행합니다. 세부 내용은 다음과 같습니다.

/var/cache/nginx : 캐시의 local disk 디렉토리 입니다.
levels : 설정된 경로 아래에 디렉토리의 계층을 설정합니다. 보통 2계층만 설정하여도 충분합니다.
key_zone : 캐시 키로 사용될 이름과 크기를 설정합니다.
max_size : 캐시 크기의 상한선을 설정합니다(선택사항).
inactive : 삭제되지 않고 캐시에 남아있을 수 있는 기간을 지정합니다.
use_temp_path=off : 임시 저장영역에 기록되어 있는 파일을 캐시될 동일한 디렉토리에 작성하도록 합니다.

2. proxy_cache_valid
valid 뒤에 오는 응답 코드에 대해 캐싱 시간을 설정해 줍니다. 응답코드 대신 any를 작성할 경우 모든 응답 코드에 대한 캐싱시간을 설정해줍니다.

이 외에도 더 많은 캐시 설정에 관련된 지시어들은 맨 아래의 참고자료 링크에서 확인해 주시면 될 것 같습니다.


## v3
```
upstream app_server {
    server 192.168.0.49:8080;
}

proxy_cache_path /var/cache/nginx levels=1:2 keys_zone=my_cache:10m max_size=10g
                 inactive=60m use_temp_path=off;


server {
    listen 80;

    location / {
        proxy_pass http://app_server;
        proxy_cache_valid  200 401 301 304  5s;
    }
}
```

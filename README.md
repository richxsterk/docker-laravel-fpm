# docker-laravel-fpm
Docker image for run Laravel on PHP FPM

## pull image from docker hub
`docker pull richxsterk/laravel-fpm:latest`

## run a single container
`docker run -d --name laravel -v yourlaravel:/code richxsterk/laravel-fpm`

## example to run along with nginx on docker compose
``` yaml
# docker-compose.yml
version: '3'
services: 
  nginx:
    image: nginx:1.19-alpine
    ports:
      - "80:80"
    volumes:
      - yourcode:/code
      - site.conf:/etc/nginx/conf.d/default.conf
  php:
    image: richxsterk/laravel-fpm:latest
    volumes:
      - yourcode:/code
  postgres:
    image: postgres:11.5-alpine
    ports:
      - "5432:5432"
    environment:
      PGDATA: /var/lib/postgresql/data
      POSTGRES_PASSWORD: postgres
    volumes:
      - yourdata:/var/lib/postgresql/data
```

## nginx config file for laravel
``` bash
# site.conf
server {
    listen 80;
    server_name laravel.test;
    root /code/public;
    index index.php;
    charset utf-8;
    client_max_body_size 20M;
    location / {
        try_files $uri $uri/ /index.php?$query_string;
        sendfile_max_chunk 512k;
    }
    location ~ \.php$ {
        include fastcgi_params;
        fastcgi_split_path_info ^(.+\.php)(/.+)$;
        fastcgi_pass php:9000;
        fastcgi_param REQUEST_METHOD $request_method;
        fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
    }
    location ~ /\.(?!well-known).* {
        deny all;
    }
}
```

## finally run docker compose up
`docker-compose up`
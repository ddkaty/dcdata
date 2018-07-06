##### 一、Smokeping
###### 1. 获取image
```
docker pull linuxserver/smokeping
```
###### 2. 创建容器
```
# docker create \
    --name smokeping5 \
    -p 8080:80 \
    -e PUID=1002 -e PGID=1002 \
    -v /opt/dcdata/smokeping/data:/data \
    -v /opt/dcdata/smokeping/etc/:/config \
    linuxserver/smokeping
# PUID和PGID为手工创建用户，如：link。-v 为指定的数据和配置目录。
```
###### 3. 启动容器
```
# docker container ls -a 
# docker container start smokeping5 
```
###### 4. 访问结果
```
http://47.106.139.161:8080/smokeping/smokeping.cgi
```
- - - 
##### 二、Nginx+php
```
# cd /opt/dcdata/nginx
# mkdir etc log html
```

```
# docker pull nginx
# docker run -it -d nginx 
# docker cp <container ID>:/etc/nginx/nginx.conf etc/
# docker cp <container ID>:/etc/nginx/conf.d etc/
```

```
[root@webapp]# cat docker-compose.yml 
version: '2'
services:
  myphp:
    image: php:7.2-fpm-alpine
    container_name: myphp
    volumes:
      - /opt/dcdata/webapp/nginx/html:/var/www/html
      - /opt/dcdata/webapp/php/etc:/usr/local/etc
    ports:
      - "9000:9000"
  nginx:
    image: nginx:alpine
    container_name: mynginx
    links: 
      - myphp
    volumes:
      - /opt/dcdata/webapp/nginx/html:/usr/share/nginx/html
      - /opt/dcdata/webapp/nginx/etc/nginx.conf:/etc/nginx/nginx.conf
      - /opt/dcdata/webapp/nginx/etc/conf.d:/etc/nginx/conf.d
      - /opt/dcdata/webapp/nginx/log:/var/log/nginx
    ports:
      - "8000:80"

```
###### 修改/opt/dcdata/webapp/nginx/etc/conf.d/default.conf

```
    location ~ \.php$ {
        root           html;
        fastcgi_pass   myphp:9000;
        fastcgi_index  index.php;
        fastcgi_param  SCRIPT_FILENAME  /var/www/html$fastcgi_script_name;
        #fastcgi_param  SCRIPT_FILENAME  $document_root$fastcgi_script_name;
        include        fastcgi_params;
    }

```

```
docker-compose up -d
```

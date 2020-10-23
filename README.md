
**docker-ubuntu-v2ray**
===========

[![](https://img.shields.io/badge/docker-ubuntu_v2ray-099cec?logo=docker)](https://hub.docker.com/r/twbworld/ubuntu-v2ray)


## 构建镜像
例 :
```shell
docker build -f dockerfile -t twbworld/ubuntu-v2ray:latest .
```

## 可安装两种V2Ray协议
* **VLess** (推荐,需准备域名,并解析)
```shell
    # 80端口用于证书验证,443是v2ray端口
    docker run --privileged -itd --name vless -v /etc/localtime:/etc/localtime:ro -p 80:80 -p 443:443 twbworld/ubuntu-v2ray:latest /sbin/init

    docker exec -it vless /bin/bash

    bash install_v2ray_vless.sh
```
> VMess的脚本需要安装Nginx并监听80端口,如果宿主机Nginx(或Nginx容器)也监听了80端口,这就会产生端口冲突;建议利用宿主机的Nginx(或Nginx容器)反向代理功能 把80端口代理到 V2Ray容器内的Nginx,例 :
>  ```shell
>    # Nginx配置
>    server {
>        listen 80;
>        listen [::]:80;
>        server_name domain.com;
>        root /usr/share/nginx/;
>        location /
>        {
>            proxy_pass http://vless:80; #代理到V2Ray容器内的Nginx, "vless"为docker的容器名称
>            proxy_http_version 1.1;
>            proxy_set_header Host $http_host;
>            proxy_set_header X-Real-IP $remote_addr;
>            proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
>            add_header X-Slave $upstream_addr;
>        }
>    }
>
>    # 如果使用的是Nginx容器,还需要跟V2Ray容器使用同一个网桥, 例 :
>    docker run --privileged -itd --name V2Ray --network my_net --ip x.x.x.x -v /etc/localtime:/etc/localtime:ro >-p 80:80 -p 443:443 twbworld/ubuntu-v2ray:latest /sbin/init
>  ```

* **VMess**
```shell
    docker run --privileged -itd --name vmess -v /etc/localtime:/etc/localtime:ro -p 12345:12345 twbworld/ubuntu-v2ray:latest /sbin/init

    docker exec -it vmess /bin/bash

    bash install_v2ray_vmess.sh
```


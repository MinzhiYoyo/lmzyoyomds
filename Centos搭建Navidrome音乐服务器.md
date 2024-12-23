---
title: Centos搭建Navidrome音乐服务器
id: 58
date: 2024-12-03 17:09:51
auther: yutanbird
cover: 
excerpt: 开源音乐服务器Navidrome，平台为Centos，使用Docker
permalink: /archives/centos%E6%90%AD%E5%BB%BAnavidrome%E9%9F%B3%E4%B9%90%E6%9C%8D%E5%8A%A1%E5%99%A8
categories:
 - comb
tags: 
 - 音乐服务器
---


# 安装docker

建议直接在宝塔面板安装

![image-20230302144858874](https://imagere.oss-cn-beijing.aliyuncs.com/img20230227/202303021448951.png)

# 配置镜像

新建`docker-compose.yml`文件，并填入一下内容：

```yaml
version: "3"
services:
  navidrome:
    image: deluan/navidrome:latest
    user: 1000:1000 # should be owner of volumes
    ports:
      - "4533:4533"  # 端口
    restart: unless-stopped
    environment:
      # Optional: put your config options customization here. Examples:
      ND_SCANSCHEDULE: 1h
      ND_LOGLEVEL: info
      ND_SESSIONTIMEOUT: 24h
      ND_BASEURL: ""
      ND_MUSICFOLDER: "/music/musics/" # 歌曲目录
      ND_DATAFOLDER: "/music/data"  # 数据库目录
```



# 配置目录

注意上面配置文件中的两个目录，需要自行新建，并且添加可写入权限，为了方便，我直接使用

```shell
chmod 777 /music/data # 不知道为什么已经有写入权限还是会报错，所以我加了777
```

# 拉取镜像

```shell
docker-compose up -d
```

## 检错方法

```shell
docker ps -a

# 应该出现下列内容
CONTAINER ID   IMAGE  COMMAND CREATED STATUS PORTS NAMES
9dd22f3b7fd4   deluan/navidrome:latest   "/app/navidrome"   17 hours ago   Up 16 hours (healthy)     0.0.0.0:4533->4533/tcp, :::4533->4533/tcp   musicserve-navidrome-1
# 其中STATUS也应该是Up xxx minutes

# 如果未启动，查看日志
docker logs 9dd22f3b7fd4 # 这个是我的ID，上面那一步可以看到

# 检查错误后，重新启动
docker restart 9dd22f3b7fd4 # 这个也是我的ID，要改成自己的
```

# 配置域名访问

&emsp;请把阿里云防火墙，宝塔面板防火墙的端口打开，端口见上面的配置文件。在宝塔中，新建一个纯静态网站并修改配置文件

``` nginx
# 这个是新家的upstream节点
upstream music {
  server 127.0.0.1:4533; # 注意端口匹配
}
server
{
    listen 80;
		listen 443 ssl http2;
    server_name xxx.lmzyoyo.top;
    index index.php index.html index.htm default.php default.htm default.html;
    root /www/wwwroot/xxx.xxxx.xxx;

    if ($server_port !~ 443){
        rewrite ^(/.*)$ https://$host$1 permanent;
    }

    ssl_certificate    /www/server/panel/vhost/cert/xxx.xxxx.xxx/fullchain.pem;
    ssl_certificate_key    /www/server/panel/vhost/cert/xxx.xxx.xxx/privkey.pem;
    ssl_protocols TLSv1.1 TLSv1.2 TLSv1.3;
    ssl_ciphers EECDH+CHACHA20:EECDH+CHACHA20-draft:EECDH+AES128:RSA+AES128:EECDH+AES256:RSA+AES256:EECDH+3DES:RSA+3DES:!MD5;
    ssl_prefer_server_ciphers on;
    ssl_session_cache shared:SSL:10m;
    ssl_session_timeout 10m;
    add_header Strict-Transport-Security "max-age=31536000";
    error_page 497  https://$host$request_uri;

    include enable-php-00.conf;

    include /www/server/panel/vhost/rewrite/xxx.xxx.xxx.conf;

    location ~ ^/(\.user.ini|\.htaccess|\.git|\.env|\.svn|\.project|LICENSE|README.md)
    {
        return 404;
    }

    location ~ \.well-known{
        allow all;
    }

    if ( $uri ~ "^/\.well-known/.*\.(php|jsp|py|js|css|lua|ts|go|zip|tar\.gz|rar|7z|sql|bak)$" ) {
        return 403;
    }
    
    # 这里删除了两个location节点，跟下面的location重复的
    
    # 这个是新加的location节点
        location ~ .*\.(gif|jpg|jpeg|png|bmp|swf)$
    {
        proxy_pass http://music;
        expires      30d;
        error_log /dev/null;
        access_log off;
        client_max_body_size 1024m;
    }

    # 这个也是新加的location节点
    location ~ .*\.(js|css)?$
    {
        proxy_pass http://music;
        expires      12h;
        error_log /dev/null;
        access_log /dev/null;
        client_max_body_size 1024m;
    }
    
    # 这个也是新加的location节点
    location / {
      proxy_pass http://music;
      proxy_set_header HOST $host;
      proxy_set_header X-Forwarded-Proto $scheme;
      proxy_set_header X-Real-IP $remote_addr;
      proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
      client_max_body_size 1024m;
    }
    
    access_log  /www/wwwlogs/xxx.xxxx.xxx.log;
    error_log  /www/wwwlogs/xxx.xxx.xxxx.error.log;
}
```

# 客户端

或者使用客户端软件 [substream](https://substreamerapp.com/)，支持`Android`与`iPhone`。

***首次登陆的用户名和密码是默认的管理员，之后可以在右上角用户中添加新的用户***


本文主要介绍`simple-obfs`插件的一项特殊用法：在有实际的web服务器存在情况下，`simple-obfs`插件可以将非正常`ss`流量重定向到此`web`服务器监听的端口，**无需域名准备**。
# 一、配置docker
## 1.1 安装docker
    
    apt update && apt -y install wget vim //升级源并安装wget和vim
    
    wget -qO- get.docker.com | bash //安装docker
    
## 1.2 配置docker
    
    systemctl start docker //启动docker
    
    systemctl status docker //查看运行状态
    
    systemctl enable docker //加入开机自启动
    
# 二、配置shadowsocks-libev
## 2.1 拉取镜像
    
    docker pull teddysun/shadowsocks-libev
    
# 2.2 创建配置文件
假定配置文件放置在`/etc/shadowsocks-libev`目录下，取名为`config.json`：
    
    {
        "server":"0.0.0.0",
        "server_port":80,
        "method":"chacha20-ietf-poly1305",
        "timeout":300,
        "password":"password0",
        "fast_open":false,
        "nameserver":"8.8.8.8",
        "mode":"tcp_and_udp",
        "plugin":"obfs-server",
        "plugin_opts":"obfs=http;failover=127.0.0.1:8080"
    }
    
> **说明**：这里用`failover=127.0.0.1:8080`将非正常ss流量重定向到本机8080端口所运行的服务。另外如果你要用obfs=tls，建议上面端口用443.

## 2.3 创建docker容器
    
    docker run -d --name ss-libev --restart always --net host -v /etc/shadowsocks-libev:/etc/shadowsocks-libev teddysun/shadowsocks-libev
    
# 三、配置caddy
## 3.1 安装caddy
    
    wget -qO- https://getcaddy.com | bash -s personal
    
## 3.2 配置caddy配置文件
假设配置文件放置在`/etc/shadowsocks-libev`目录下，取名为`caddy.conf`，假设你的vps ip是`1.2.3.4`，那么caddy配置文件可以这么写：
    
    1.2.3.4:8080 {
      gzip
      bind 127.0.0.1
      log /etc/shadowsocks-libev/caddy.log
      proxy / http://baidu.com
    }
    
## 3.3 创建caddy服务
在`/etc/systemd/system`目录下创建`caddy.service`文件，内容如下：
    
    [Unit]
    Description=Caddy Service
    After=network.target
    
    [Service]
    ExecStart=/usr/local/bin/caddy -conf /etc/shadowsocks-libev/caddy.conf
    Restart=on-abort
    LimitNOFILE=1048576
    
    [Install]
    WantedBy=multi-user.target
    
## 3.4 配置caddy服务
    
    systemctl daemon-reload
    
    systemctl start caddy //启动服务
    
    systemctl status caddy //查看状态
    
    systemctl enable caddy //加入开机自启动
    
在`caddy`服务和ss容器都启动之后，你可以客户端里配置ss节点了，然后在浏览器里输入`http://1.2.3.4`，你会发现它会跳转到你设置的网站上。
# 四、后续容器更新
## 4.1 重新拉取镜像
    
    docker pull teddysun/shadowsocks-libev
    
> **说明**：秋水逸冰（秋大）维护镜像还是很频繁的，在这点上给秋大点赞。

## 4.2 容器相关操作
在2.3那一步，`--name ss-libev`中，`ss-libev`是这个容器的识别符，这里记作`$name=ss-libev`，那么：
    
    docker stop $name //停止正在运行中的容器
    
    docker rm $name //移除容器
    
    docker image ls //注意查看标签变为none的旧版ss docker镜像id，这里假设其为123asd123
    
    docker image rm 123 //移除旧镜像
    
    docker run -d --name ss-libev --restart always --net host -v /etc/shadowsocks-libev:/etc/shadowsocks-libev teddysun/shadowsocks-libev
    
>**参考链接**：
- https://github.com/shadowsocks/simple-obfs
- https://hub.docker.com/r/teddysun/shadowsocks-libev
- https://bbs.letitfly.me/d/301

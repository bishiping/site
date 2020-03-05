本文主要介绍使用`秋水逸冰`制作的`teddysun/shadowsocks-libev`镜像快速部署`shadowsocks-libev`，镜像内置可选`obfs插件`和`v2ray-plugin插件`。

条件准备：
- 一台墙外vps
- 一个域名
- 熟悉基本linux命令

# 一、安装docker
## 1.1 以`root`用户登录，执行下面命令安装`docker`
    
    apt update && apt -y install wget vim //升级源并安装必要软件
    
    wget -qO- get.docker.com | bash //安装docker
    
## 1.2 对docker的一些命令操作
    
    docker version 或者 docker -v //查看docker版本信息
    
    systemctl start docker //启动docker服务
    
    systemctl status docker //查看docker运行状态
    
    systemctl enable docker //添加开机自启动
    
# 二、配置文件
## 2.1 配置文件取名`config.json`，放在`/etc/shadowsocks-libev`目录下
配置范例如下：
    
    {
        "server":"0.0.0.0",
        "server_port":9000,
        "password":"password0",
        "timeout":300,
        "method":"aes-256-gcm",
        "fast_open":false,
        "nameserver":"8.8.8.8",
        "mode":"tcp_and_udp"
    }
    
如果你想同时启用`simple-obfs`插件，那么配置文件范例如下：
    
    {
        "server":"0.0.0.0",
        "server_port":9000,
        "password":"password0",
        "timeout":300,
        "method":"aes-256-gcm",
        "fast_open":false,
        "nameserver":"8.8.8.8",
        "mode":"tcp_and_udp",
        "plugin":"obfs-server",
        "plugin_opts":"obfs=tls或者obfs=http"
    }
    
如果你想同时启用`v2ray-plugin`插件，那么配置文件范例如下：
    
    {
        "server":"0.0.0.0",
        "server_port":9000,
        "password":"password0",
        "timeout":300,
        "method":"aes-256-gcm",
        "fast_open":false,
        "nameserver":"8.8.8.8",
        "mode":"tcp_and_udp"，
        "plugin":"v2ray-plugin",
        "plugin_opts":"server或者server;tls;host=yourdomain.com;cert=/path/to/cert.crt;key=/path/to/cert.key"
     }
     
 > 关于证书：使用acme.sh脚本申请安装证书这里不再赘述，更多细节请参考 https://acme.sh
 
 # 三、配置容器
 ## 3.1 拉取镜像
    
    docker pull teddysun/shadowsocks-libev
    
 ## 3.2 启动容器
    
    docker run -d --name ss-libev --restart always -p 9000:9000 -p 9000:9000/udp -v /etc/shadowsocks-libev:/etc/shadowsocks-libev teddysun/shadowsocks-libev
    
## 3.3 容器后续更新
记前面`ss-libev`为容器识别符，即`$name=ss-libev`，
    
    docker stop $name //停止正在运行中的容器
    
    docker rm $name //移除容器识别符为$name的容器
    
    docker pull teddysun/shadowsocks-libev //此为重新拉取新版镜像
    
    docker image ls 或者 docker images //注意查看旧版镜像的ID，假设为123asd23d56
    
    docker image rm 123 或者 docker rmi 123 //移除旧版镜像，123为镜像ID的前三位
    
    docker run -d --name ss-libev --restart always -p 9000:9000 -p 9000:9000/udp -v /etc/shadowsocks-libev:/etc/shadowsocks-libev teddysun/shadowsocks-libev
    
> 参考链接：
- https://teddysun.com/536.html
- https://hub.docker.com/r/teddysun/shadowsocks-libev/
- https://github.com/shadowsocks

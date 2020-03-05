本文主要介绍`caddy`的安装和使用。
# 一、安装
    
    wget -qO- https://getcaddy.com | bash -s personal
    
# 二、配置文件
## 2.1 caddy配置文件示例
一般取名为`Caddyfile`，这里假定你的域名为`yourdomain.com`：
    
    yourdomain.com:80 {
      redir https://yourdomain.com{url}
    }
    
    yourdomain.com:443 {
      gzip
      tls /path/to/cert.crt /path/to/cert.key
      log ~/caddy.log
    }
    
这里是手动指定的证书路径，你也可以直接给出邮箱用于自动更新证书通知，但是那样会自动将80端口重定向到`https`
## 2.2 服务配置
这里创建`caddy.service`服务文件，放置在`/etc/systemd/system`目录下，便于对caddy的控制。
    
    [Unit]
    Description=Caddy Service
    After=network.target
    [Service]
    ExecStart=/usr/local/bin/caddy -conf ~/Caddyfile
    Restart=on-abort
    LimitNOFILE=1048576
    [Install]
    WantedBy=multi-user.target
    
## 3.3 启动caddy服务
    
    systemctl start caddy //启动caddy
    
    systemctl status caddy //查看caddy运行状态
    
    systemctl enable caddy //加入开机自启动

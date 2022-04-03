本文是参考网上相关教程，以github上`shadowsocks-libev`最新源码进行编译安装。
# 一、安装依赖
    
    apt -y install git vim gettext build-essential autoconf libtool libpcre3-dev libev-dev libc-ares-dev automake libmbedtls-dev libsodium-dev
    
# 二、克隆源码
    
    git clone https://github.com/shadowsocks/shadowsocks-libev.git
    
    cd shadowsocks-libev
    
    git submodule update --init --recursive //下载子模块
    
# 三、编译
    
    ./autogen.sh
    
    ./configure --disable-documentation
    
    make && make install
    
# 四、配置文件
`shadowsocks-libev`配置文件，建议放置在`/etc/shadowsocks-libev/config.json`

    {
        "server":"0.0.0.0",
        "server_port":8388,
        "timeout":300,
        "password":"password",
        "method":"aes-256-gcm",
        "fast_open":true,
        "nameserver":"8.8.8.8",
        "mode":"tcp_and_udp"
    }
    
# 五、systemd服务配置
在/etc/systemd/system目录下创建`shadowsocks-libev.service`服务文件：
    
    [Unit]
    Description=Shadowsocks-libev
    After=network.target
    [Service]
    Type=simple
    User=nobody
    ExecStart=/usr/bin/ss-server -c /etc/shadowsocks-libev/config.json
    [Install]
    WantedBy=multi-user.target
    
>参考链接：
- https://github.com/shadowsocks/shadowsocks-libev
- https://bbs.letitfly.me/d/301
- https://0vo.moe/posts/%E7%BC%96%E8%AF%91%E5%AE%89%E8%A3%85-shadowsocks-libev-%E8%AE%B0%E5%BD%95/

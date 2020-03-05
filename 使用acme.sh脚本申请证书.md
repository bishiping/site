# 一、安装acme.sh脚本
以`root`用户登录linux主机，使用下面命令安装脚本：
    
    apt update && apt -y install socat //升级源并安装socat
    
    wget -qO- get.acme.sh | bash //安装此脚本
    
    source ~/.bashrc //让别名生效
    
# 二、配置证书
## 2.1 获取Cloudflare API Key
注册好`Cloudflare`账号，假定域名是`yourdomain.com`，且已经添加至`Cloudflare`进行域名托管，获取`Global API Key`。
## 2.2 提前配置变量
    
    export CF_Key="slfjksjffjfhfhkjhfksjf" //此处替换成你自己的Key
    export CF_Email="yourcloudflare@gmail.com" //此处填写你注册Cloudflare使用的邮箱账号
    
## 2.3 申请证书
    
    acme.sh --issue --dns dns_cf -d yourdomain.com -k ec-256
    
## 2.4 安装证书到指定位置
假定linux主机里已有`/root/ssl`目录，现在要把证书和key安装到此目录下，那么执行下面的命令即可，
    
    acme.sh --installcert -d yourdomain.com --fullchain-file /root/ssl/web.crt --key-file /root/ssl/web.key --ecc
    
# 三、证书更新
从letscncrypt申请到的证书有效期是90天，但脚本会每60天会对证书进行更新，后续可能会缩短这个时间，都是自动的，无需更新。
# 四、脚本更新问题
目前由于acme协议和letsencrypt ca都在频繁更新，所以`acme.sh`脚本也需要经常进行更新以保持同步。
## 4.1 升级脚本到最新版
    
    acme.sh --upgrade
    
 ## 4.2 设置脚本自动升级
    
    acme.sh --upgrade --auto-upgrade
    
## 4.3 设置关闭脚本自动更新
    
    acme.sh --upgrade --auto-upgrade 0
    
# 五、定时任务
## 5.1 安装crontab
    
    apt -y install cron
    
## 5.2 设置定时任务
    
    crontab -e //编辑定时任务
    
    30 12 1 */2 * acme.sh --installcert -d yourdomain.com --fullchain-file /root/ssl/web.crt --key-file /root/ssl/web.key --ecc
    
这是设置每隔2个月在1号的12:30安装证书和key到/root/ssl目录下
  
>参考链接：https://github.com/acmesh-official/acme.sh

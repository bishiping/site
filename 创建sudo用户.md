# 一、添加用户
以**root**用户登录linux主机
    
    useradd -m user //user代表设置的用户名
    
    passwd user //给user用户设置密码
    
# 二、安装sudo
Debian/Ubuntu系统执行下面命令安装sudo
    
    apt update && apt -y install sudo

Centos用户执行下面命令安装sudo
    
    yum update && yum -y install sudo
    
# 三、添加用户为sudo用户
    
    cd /etc/sudoers.d //切换工作目录
    
    vim user //创建以user用户名为名的文件
    
    user ALL=(ALL) ALL //添加此行保存文件
    user ALL=(ALL) NOPASSWD: ALL //或者添加此行保存文件
    
    chmod 440 /etc/sudoers.d/user //设置正确权限
    
    usermod -s /bin/bash user //指定user用户shell为/bin/bash
    

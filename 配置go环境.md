本文主要介绍`linux_amd64`下`go`环境的配置。
# 一、下载go-linux的包
执行下面的命令进行下载：
    
    cd /root && curl -O https://dl.google.com/go/go1.14.linux-amd64.tar.gz
    
> **说明**：最新版`go-linux`的包请前往这个网站下载：https://golang.org/dl/

# 二、进行解压操作
执行下面的命令进行解压：
    
    tar -C /usr/local -zxf go1.14.linux-amd64.tar.gz //解压压缩包到/usr/local目录下
    
    rm go1.14.linux-amd64.tar.gz //可以选择删除
    
# 三、配置go环境变量
    
    vim ~/.bash_profile //编辑此文件
    
    //将下面内容添加至该文件
    export GOPATH=$HOME/work
    export PATH=$PATH:/usr/local/go/bin:$GOPATH/bin
    //将上面内容添加至文件后保存文件
    
    source ~/.bash_profile //使设置生效
    
    go version //查看go的版本号
    
至此，即完成go的环境配置。

# CentOS/Linux 安装shadowsocks 翻墙
CentOS/Linux 安装shadowsocks 翻墙

工作原因， 经常需要上google查资料， 所以不得不翻墙， 因为工作基本都是用的centos/linux， 将其配置过程记录下来， 以备忘或帮助有相同需求的同学。

1.  安装shadowsocks

    a. 自动安装
        Debian / Ubuntu:
        
            apt-get install python-pip
            
            pip install shadowsocks
                        
        CentOS:
            yum install python-setuptools && easy_install pip
            
            pip install shadowsocks

    b. 手动安装
        下载shadowsocks源码：https://pypi.python.org/packages/02/1e/e3a5135255d06813aca6631da31768d44f63692480af3a1621818008eb4a/shadowsocks-2.8.2.tar.gz
        解压后， python setup.py install

2. 安装 privoxy

    a. 先下载privoxy源码：
    
        wget -c http://www.privoxy.org/sf-download-mirror/Sources/3.0.26%20%28stable%29/privoxy-3.0.26-stable-src.tar.gz
        
    b. 建立privoxy用户：
    
        useradd privoxy
        
    c. 直接编译安装：   
    
        ./configure && make && make -s install

3. 配置shadowsocks

    a. 首先确保您有购买VPN
    
        如有需要可以参考一下红杏出墙的VPN， 本人用了一年多， 效果不错：
        
        https://my.yizhihongxing.com/aff.php?aff=7959
        
    b. 配置proxy环境变量， 编译/etc/profile中添加以下内容
    
        export http_proxy=http://127.0.0.1:8118
        
        export https_proxy=http://127.0.0.1:8118
        
        export no_proxy=localhost

    c. 添加VPN配置文件
    
    vim /etc/shadowsocks.json
    
    {

    "server":"您的VPN服务器地址",

    "server_port":您的VPN端口,

    "local_address": "127.0.0.1",

    "local_port":1080,

    "password":"您的VPN密码",

    "timeout":300,

    "method":"rc4-md5",

    "fast_open": false,

    "workers": 4

    }

4. 添加启动脚本

    vim /usr/bin/myss

    #!/bin/bash

    case $1 in

    start)

    nohup sslocal -c /etc/shadowsocks.json &>> /var/log/shadowsocks.log &

    service privoxy start

    export http_proxy=http://127.0.0.1:8118

    export https_proxy=http://127.0.0.1:8118

    export ftp_proxy=http://127.0.0.1:8118

    export no_proxy=localhost

    ;;

    stop)

    unset http_proxy https_proxy no_proxy

    service privoxy stop

    pkill sslocal

    ;;

    reload)

    pkill sslocal

    nohup sslocal -c /etc/sysconfig/.shadowsocks.json &>> /var/log/shadowsocks.log &

    ;;

    set)

    export http_proxy=http://127.0.0.1:8118

    export https_proxy=http://127.0.0.1:8118

    export ftp_proxy=http://127.0.0.1:8118

    export no_proxy=localhost

    ;;

    unset)

    unset http_proxy https_proxy no_proxy ftp_proxy

    ;;

    *)

    echo 'usage start|stop|reload|set|unset'

    exit 1

    ;;

    esac

5. 命令myss start / stop 即可打开关闭shadowsocks

6. 配置firefox用VPN上网

![image](https://github.com/mingzhi198/CentOS-Linux-shadowsocks/blob/master/firefox-shadowsocks.png)

    a. 首选项－> 高级 －> 网络 －> 设置 －> 配置访问国际互联网的代理
    b. 选中 “手动配置代理“，
        HTTP 代理项填： 127.0.0.1 ， 端口： 8118
    c. 选中 “为所有协议使用相同代理“, 确定即可

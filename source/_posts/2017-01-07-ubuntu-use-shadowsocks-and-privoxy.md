---
layout: post
title: "Ubuntu 使用 ShadowSocks + Privoxy 代理"
date: 2017-01-07 12:35:22 +0800
comments: true
categories: 经验分享 科学上网
---
## 引言

先说说这东西有什么用吧，我是 Windows7 使用 Vagrant 安装了个 Ubuntu 虚拟机，然后需要在虚拟机里面配置 SS 代理使用 PHP 的 Composer 下载，不然非常慢。
那么本教程应该同样适用于 Ubuntu 服务器（做为 SS 客户端的方式）配置使用 SS，其他版本的 Linux 要想使用需要稍微改动下，但是思路是一样的。

如果你的 Mac 电脑，终端需要配置 SS 代理，推荐你使用 proxychains-ng 方式，具体查看[macOS 终端走代理（科学上网）](https://gold.xitu.io/entry/5821840cd203090055134cc0)

## ShadowSocks 客户端

注意是 ShadowSocks 客户端，服务端我就不介绍了。先安装 Python pip 再安装 shadowsocks，然后再配置：

```
sudo apt-get install python-pip
sudo pip install shadowsocks
sudo ln -s /usr/local/python/bin/sslocal /usr/bin/sslocal
sudo vim /etc/shadowsocks.conf
```

配置文件代码如下：

```
{
    "server":"your_server_ip",      #ss服务器IP
    "server_port":your_server_port, #端口
    "local_address": "127.0.0.1",   #本地ip
    "local_port":1080,              #本地端口
    "password":"your_server_passwd",#连接ss密码
    "timeout":300,                  #等待超时
    "method":"rc4-md5",             #加密方式
    "fast_open": false,             # true 或 false。如果你的服务器 Linux 内核在3.7+，可以开启 fast_open 以降低延迟。开启方法： echo 3 > /proc/sys/net/ipv4/tcp_fastopen 开启之后，将 fast_open 的配置设置为 true 即可
    "workers": 1                    # 工作线程数
}
```
<!--more-->

开启：

```
# 启动 SS
sudo nohup sslocal -c /etc/shadowsocks.conf >/dev/null 2>%1 &
# 查看进程
sudo ps aux |grep sslocal |grep -v "grep"
```

```
# 添加开启启动
sudo su
echo "nohup sslocal -c /etc/shadowsocks.conf /dev/null 2>&1 &" >> /etc/rc.local
```


## 安装 Privoxy

```
sudo apt-get install privoxy -y

```

修改配置文件

```
sudo cp /etc/privoxy/config /etc/privoxy/config.bak
sudo vim /etc/privoxy/config
```

找到 `listen-address` 确保有这行代码 `listen-address 127.0.0.1:8118`

找到 `forward-socks5` 确保有这行代码(没有自己加) `forward-socks5  /  127.0.0.1:1080 .`

启动

```
sudo service privoxy start
sudo service privoxy status
```

配置转发

```
sudo vim ~/.bashrc
```

在最后添加如下代码：

```
export http_proxy="http://127.0.0.1:8118"
export https_proxy="http://127.0.0.1:8118"
```

重载配置

```
source ~/.bashrc
```

## 测试

```
curl ip.gs
```

## 参考链接

- [Linux中使用ShadowSocks+Privoxy代理](https://docs.lvrui.io/2016/12/12/Linux%E4%B8%AD%E4%BD%BF%E7%94%A8ShadowSocks-Privoxy%E4%BB%A3%E7%90%86/)
- [linux下的ss+privoxy代理配置](http://www.voidcn.com/blog/xwydq/article/p-5796260.html)
- [Linux 命令行下使用 Shadowsocks 代理](https://mritd.me/2016/07/22/Linux-%E5%91%BD%E4%BB%A4%E8%A1%8C%E4%B8%8B%E4%BD%BF%E7%94%A8-Shadowsocks-%E4%BB%A3%E7%90%86/)

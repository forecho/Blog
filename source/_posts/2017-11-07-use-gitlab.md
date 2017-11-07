---
layout: post
title: "使用 GitLab"
date: 2017-11-07 16:01:58 +0800
comments: true
categories: 技术分享 GitLab
---

## 安装 GitLab

```
$ sudo apt-get install curl openssh-server ca-certificates postfix
$ curl -sS http://packages.gitlab.com.cn/install/gitlab-ce/script.deb.sh | sudo bash
$ sudo apt-get install gitlab-ce
```

修改 GitLab 配置文件

```
$ sudo vim /etc/gitlab/gitlab.rb
```

修改之后的配置如下：

<!--more-->

```
external_url 'http://repo.yd.com';
web_server['external_users'] = ['www-data']
nginx['enable'] = false // 关闭自带nginx
```

修改时区

```
$ sudo vim /var/opt/gitlab/gitlab-rails/etc/gitlab.yml
```

修改后为： `time_zone : 'Beijing'`

然后重启 GitLab

```
$ sudo gitlab-ctl reconfigure
$ sudo gitlab-ctl restart
```

## 配置 nginx

GitLab 默认是集成了 nginx，但是我们一般不用他的，上面修改配置的时候，我们已经把 GitLab 集成的 nginx 关闭了。下面我们说一下如何使用 Linux 的 nginx。

添加 gitlab nginx 配置文件

```
$ sudo vim /etc/nginx/conf.d/gitlab.conf
```

添加如下代码：

```
upstream gitlab-workhorse {
  server unix:/var/opt/gitlab/gitlab-workhorse/socket;
}
server {
  listen 80;
  # 此处域名可以根据情况修改，其它地方不用改
  server_name repo.yd.com;
  server_tokens off;
  root /opt/gitlab/embedded/service/gitlab-rails/public;
  access_log  /var/log/nginx/gitlab_access.log;
  error_log   /var/log/nginx/gitlab_error.log;
  location / {
    client_max_body_size 0;
    gzip off;
    proxy_read_timeout      300;
    proxy_connect_timeout   300;
    proxy_redirect          off;
    proxy_http_version 1.1;
    proxy_set_header    Host                $http_host;
    proxy_set_header    X-Real-IP           $remote_addr;
    proxy_set_header    X-Forwarded-For     $proxy_add_x_forwarded_for;
    proxy_set_header    X-Forwarded-Proto   $scheme;
    proxy_pass http://gitlab-workhorse;
  }
}
```

推出 vim， 使用下面命令确认 nginx 配置是否正确：

```
$ sudo nginx -t -c /etc/nginx/nginx.conf
```

重启 nginx

```
$ sudo service nginx reload
```

## 配置 Email

修改配置文件

```
$ sudo vim /etc/gitlab/gitlab.rb
```

依次修改以下几处

```
gitlab_rails['smtp_enable'] = true
gitlab_rails['smtp_address'] = "smtpdm.aliyun.com"
gitlab_rails['smtp_port'] = 25
gitlab_rails['smtp_user_name'] = "gitlab@no-reply.forecho.com"
gitlab_rails['smtp_password'] = "xxxxxx"
gitlab_rails['smtp_domain'] = "no-reply.forecho.com"
gitlab_rails['smtp_authentication'] = "login"
gitlab_rails['smtp_enable_starttls_auto'] = true
gitlab_rails['smtp_tls'] = false

# ...
gitlab_rails['gitlab_email_from'] = "gitlab@no-reply.forecho.com"
```

重启

```
$ sudo gitlab-ctl reconfigure
$ sudo gitlab-ctl restart
```

测试

```
$ sudo gitlab-rails console
// 发送测试邮件
$ Notify.test_email('收件人邮箱', '邮件标题', '邮件正文').deliver_now
```

## 总结

GitLab 基本是一个互联网企业的标准配置了，免费，功能强大。这篇文章主要是记录自己在公司搭建 GitLab 的过程，包括基本安装，不使用自身集成的 nginx，配置邮件服务。

后面我会再介绍 GitLab 的 CI 使用。
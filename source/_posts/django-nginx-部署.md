---
title: django 生产环境部署
tags:
  - django
categories:
  - 技术文章
toc: true
draft: 'true'
translate_title: django-production-environment-deployment
date: 2019-06-21 11:28:04
---

> 使用`nginx+uwsgi`实现django生产环境的部署

## 目标部署环境
- 系统: ubuntu 18.04
- uwsgi + django + nginx

<!-- more -->

## 准备工作

1. 在Django工程路径下执行 `freeze > requirement.txt` 将需要用到的插件列表保存到文件中
2. 将Django上传到git

## 部署过程
### python虚拟环境安装配置
暂无

### django配置

1. 在工程路径下执行 `pip install -r requirement.txt` 安装需要的插件
2. 执行 `python managy.py collectstatic`将静态文件收集到工程的 `static`路径下
3. 执行以下指令，配置数据库
    ```
    python managy.py makemigrations
    python managy.py migrate
    ```

### uwsgi安装配置
1. 安装uwsgi并进行测试
```Bash
uwsgi --http 0.0.0.0:9099 --chdir /root/dj-sites/yuTa/yuTa/projects --module projects.wsgi --home /root/dj-sites/yuTa/venv
```
  - http 指定端口号
  - chdir 指定工程路径，即manager.py所在路径
  - module 用于指定wsgi.py文件相对工程路径的位置
  - home 指定虚拟机所在路径

2. 配置uwsgi.ini文件
```
[uwsgi]
chdir           = /root/dj-sites/yuTa/yuTa/projects
module          = projects.wsgi
master          = true
processes       = 4
socket          = /root/dj-sites/yuTa/yuTa.sock
#http            = :9099
vacuum          = true
home            = /root/dj-sites/yuTa/venv

stats           = /root/dj-sites/yuTa/uwsgi.status
pidfile         = /root/dj-sites/yuTa/uwsgi.pid
```

3. uwsgi命令 
- 启动：`uwsgi --ini xxx.ini`
- 重启：`uwsgi --reload xxx.pid`
- 停止：`uwsgi --stop xxx.pid`

### nginx配置
在`/etc/nginx/sites-available`路径下创建`yuTa_nginx`
```
upstream django {
        server 127.0.0.1:9092;
}
server {
        listen      80;
        server_name yuTa.lazyboy.site;
        charset     utf-8;

        client_max_body_size 75M;   # adjust to taste
        location /media  {
            alias /root/dj-sites/yuTa/yuTa/projects/media;
        }
        location /static {
            alias /root/dj-sites/yuTa/yuTa/projects/static;
        }
        location / {
            uwsgi_pass  django;
            include     /etc/nginx/uwsgi_params;
        }
}

```
然后执行
执行`ln -s yuTa_nginx ../sites-enabled/yuTa_nginx`使其生效

## https配置
暂无	 
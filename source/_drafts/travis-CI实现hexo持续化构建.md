---
title: travis CI实现hexo持续化构建
tags:
  - hexo
categories:
  - 随手小记
toc: true
translate_title: travis-ci-implements-hexo-persistence-build
permalink: 1562569033
abbrlink: a40fe5e6
date: 2019-07-08 14:57:13
---

## 注册Travis CI
1. 注册 [Travis CI](https://www.travis-ci.org/) 账号，可直接使用github授权登录
2. 打开文章源码仓库开关
3. 设置 -- 添加权限
4. github生成token
5. Travis 添加github的token

## 添加构建配置文件

在hexo根目录下添加`.travis.yml`文件，文件内容如下

```Bash
language: node_js
node_js: stable

# Travis-CI Caching
cache:
  directories:
    - node_modules

before_install:
  - npm install hexo-cli -g

install:
  - npm install

script:
  - hexo clean
  - hexo g


after_script:
  - cd ./public
  - git init
  - git config user.name "xbotao"
  - git config user.email "xbotao2014@163.com"
  - git add .
  - git commit -m "Travis CI Auto Builder :$(date '+%Y-%m-%d %H:%M:%S' -d '+8 hour')"
  - git push --force --quiet "https://${GH_TOKEN}@${GH_REF}" master:master

branches:
  only:
    - master # 只对master分支构建

env: # 环境变量
 global:
   - GH_REF: github.com/xbotao/xbotao.github.io.git
```

---
本文为草稿，未进行细化

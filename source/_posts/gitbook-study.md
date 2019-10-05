---
title: Gitbook
layout: page
date: 2019-10-04
toc: true
categories: 
- Gitbook
tags: 
- GitBook
---
掌握了md语法后，如何构建一本电子书呢，经过调研，其实也特别简单，主要的步骤如下
<!-- more-->
- npm下载(淘宝源)：npm --registry https://registry.npm.taobao.org install gitbook-plugin-toggle-chapters
- 安装gitbook： npm install -g gitbook-cli
- 安装gitbook插件： gitbook install
- gitbook发布：gitbook serve --lrport 35288 --port 4001
- 打成war包：jar -cvf  dist.war *  (提前cd到_book目录)
- 解压war包：jar -xvf dist.war （rz上传命令）
- 上传到服务器上（ip:10.143.135.161,user:yxgly,pwd:Abcd1234）
- 寻找nginx配置文件：nginx -t
- 重新加载nginx：service nginx reload
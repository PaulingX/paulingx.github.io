---
title: LskyPro 图床安装与配置指南及问题解决
date: 2024-12-25 23:15:21
tags:  docker
comments: true
categories: 工具
cover: ../images/2023/676c2192a559c.png
---
# LskyPro 图床安装与配置指南 
<!--more-->
## 1. Docker 安装

使用 Docker 一键部署 LskyPro：

```bash
docker run -d \
  --name lsky-pro \
  --restart unless-stopped \
  -p 40027:8089 \
  -v /mnt/sda3/Configs/lsky-pro/data:/var/www/html \
  -e WEB_PORT=8089 \
  halcyonazure/lsky-pro-docker:latest
```

## 2. 初始化配置

1. 访问管理页面：`http://127.0.0.1:40027`
2. 按照界面提示完成初始化设置（部分图片引用[该文章](https://zhuanlan.zhihu.com/p/667419644)）：

![初始化界面](../images/2023/676c1b4ed5873.png)
![基础配置](../images/2023/676c1b83c766f.png)
![管理员账户设置](../images/2023/676c1bade69dc.png)

## 3. 配置 Alist WebDAV 存储

### 3.1 创建存储策略
1. 进入「储存策略」页面
2. 点击「创建储存策略」
3. 配置 WebDAV 参数：

![存储策略配置](../images/2023/676c1d1745843.png)

### 3.2 测试上传
上传测试图片验证配置：

![上传测试](../images/2023/676c1daf703e2.png)

## 4. 问题及解决

### 4.1 图片 404 问题
**问题描述**：图片可以上传，但访问时出现 404 错误。
**原因**：Alist 的真实路径中包含 `/d` 路径。

![404错误说明](../images/2023/676c1efe91fa3.png)

### 4.2 解决方案
使用 Nginx/Lucky 进行路径重定向：

![重定向配置](../images/2023/676c1f9444ff7.png)

### 4.3 报错401
需要在alist中去掉签名
![去掉签名](https://img.flygo.site:8088/img/2025/07/26/688477b3d4ec0.png)

## 参考资料
- [LskyPro 文档](https://docs.lsky.pro/)
- [图片 404 问题解决方案](https://github.com/lsky-org/lsky-pro/issues/520)
---
title: 使用Rclone备份数据到云存储
date: 2025-08-06 20:43:48
tags: OpenWrt
comments: true
categories: 工具
---
### 一、Rclone是什么？

Rclone 是一个用于管理云存储上文件的命令行程序。它是云供应商 Web 存储接口的功能丰富的替代方案。Rclone 支持 70 多种云存储产品，包括 S3 对象存储、业务和消费者文件存储服务，以及标准传输协议。<!--more-->
### 二、Rclone能做什么？

- 将文件备份（和加密）到云存储
- 从云存储恢复（和解密）文件
- 将云数据镜像到其他云服务或本地
- 将数据迁移到云或在云存储供应商之间迁移数据
- 将多个加密、缓存或多样化的云存储挂载为磁盘
- 使用 lsf、ljson、size、ncdu 分析和核算云存储上保存的数据
- 将文件系统合并在一起，将多个本地和/或云文件系统作为一个系统呈现
### 三、安装步骤

#### Linux/macOS 安装

```bash
# 使用curl安装（通用方法）
curl https://rclone.org/install.sh | sudo bash

# 或者使用包管理器安装
# Debian/Ubuntu
sudo apt install rclone

# CentOS/RHEL/Fedora
sudo yum install rclone
# 或者新版本使用
sudo dnf install rclone
```

#### Windows 安装

1. [下载 Windows 版本](https://rclone.org/downloads/)
2. 解压下载的压缩包
3. 将 rclone.exe 添加到系统 PATH 环境变量中
4. Windows 下挂载功能需要安装 [WinFsp](https://winfsp.dev/rel/)

### 四、配置云存储

```bash
rclone config
```

按照提示选择云存储类型（如 Google Drive、OneDrive、Dropbox、WebDAV 等），然后按照授权流程完成设置。

### 五、常用命令

#### 基本操作命令

```bash
# 简单复制（只复制新增或修改的文件）
rclone copy /本地/目录 远程名称:远程路径

# 移动文件（复制后删除源文件）
rclone move 源 目标

# 同步（保持两边一致，会删除目标中多余的文件）
rclone sync /本地/目录 远程名称:远程路径

# 列出指定路径下的目录
rclone lsd 远程名称:远程路径

# 查看云存储文件占用大小
rclone size 远程名称:远程路径

# 挂载远程存储为本地磁盘（需要 WinFsp 支持）
rclone mount 远程名称:远程路径 X: --vfs-cache-mode full
```

#### 常用参数

```bash
-P, --progress              # 显示实时传输进度
--transfers N               # 并行文件传输数，默认为4
--config string             # 指定配置文件路径
--update                    # 增量备份，跳过目标中较新的文件
--dry-run                   # 试运行，显示将要执行的操作但不实际执行
--exclude pattern           # 排除匹配模式的文件
--filter pattern            # 过滤文件（包含或排除）
--bwlimit speed             # 限制带宽，例如 1M
--checksum                  # 使用校验和而不是修改时间来检测文件变化
```

### 六、加密功能

Rclone 提供了强大的加密功能，可以对文件内容和文件名进行加密：

```bash
# 配置加密远程
rclone config
# 选择 "crypt" 类型
# remote 设置为已配置的云存储，如 "mydrive:/backup"
# filename_encryption 设置为 "standard"
# directory_name_encryption 设置为 "true"
# password 设置加密密码
# password2 设置 salt 密码
```

使用加密远程与普通远程一样：

```bash
# 同步到加密远程
rclone sync /本地/目录 加密远程名称:路径

# 挂载加密远程
rclone mount 加密远程名称:路径 X: --vfs-cache-mode full
```

### 七、定时同步

#### Linux/macOS - 使用 crontab

```bash
# 编辑定时任务
crontab -e

# 添加任务（每天中午12点执行同步）
0 12 * * * rclone copy /本地/目录 webdav:/备份目录 >> /opt/rclone/logs/rclone.log 2>&1

# 保存后重新加载配置
systemctl reload crond.service

# 重启服务（如需要）
systemctl restart crond.service

# 查看定时任务是否生效
crontab -l
```

#### Windows - 使用任务计划程序

1. 打开"任务计划程序"
2. 点击"创建基本任务"
3. 输入任务名称和描述
4. 选择触发器（如每天、每周等）
5. 选择操作为"启动程序"
6. 程序选择 rclone.exe
7. 参数添加：`copy "C:\本地目录" webdav:/备份目录`
8. 完成设置


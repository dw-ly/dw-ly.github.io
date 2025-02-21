---
title: Docker环境配置指南
date: 2024-02-21
categories:
  - 环境
tags:
  - Docker
  - 容器化
  - 环境配置
---

## Docker 环境搭建基础

本文介绍 Docker 环境的基本配置和使用方法。

### 1. 安装 Docker

Windows 环境下的安装步骤：

```bash
# 下载 Docker Desktop
# 从 https://www.docker.com/products/docker-desktop 下载

# 验证安装
docker --version
docker-compose --version
```

### 2. 基本概念

- 镜像（Image）：容器的模板
- 容器（Container）：运行中的实例
- Dockerfile：构建镜像的脚本

### 3. 常用命令

```bash
# 拉取镜像
docker pull nginx

# 运行容器
docker run -d -p 80:80 nginx

# 查看运行中的容器
docker ps

# 停止容器
docker stop container_id
```

更多 Docker 使用技巧将在后续文章中详细介绍。

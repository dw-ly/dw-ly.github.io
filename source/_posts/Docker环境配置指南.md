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

# Docker环境配置指南

## 安装配置

### Windows 环境安装

```bash
# 下载 Docker Desktop
# 从 https://www.docker.com/products/docker-desktop 下载

# 验证安装
docker --version
docker-compose --version
```

## 基本概念

- 镜像（Image）：容器的模板
- 容器（Container）：运行中的实例
- Dockerfile：构建镜像的脚本

## 常用命令

### 容器操作

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

### 镜像管理

```bash
# 查看本地镜像
docker images

# 删除镜像
docker rmi image_name

# 构建镜像
docker build -t name:tag .
```

## 进阶使用

### Docker Compose

```yaml
version: '3'
services:
  web:
    image: nginx
    ports:
      - "80:80"
  db:
    image: mysql
    environment:
      - MYSQL_ROOT_PASSWORD=secret
```

更多 Docker 使用技巧将在后续文章中详细介绍。

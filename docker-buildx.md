---
title: docker buildx
description: docker编译多环境镜像
published: true
date: 2023-04-04T08:19:16.395Z
tags: 
editor: markdown
dateCreated: 2023-04-04T08:19:16.395Z
---

# 基础配置
```
# 创建构建容器
docker buildx create --name myBuild
# buildx使用构建容器
docker buildx use myBuild
# 初始化构建容器
docker buildx inspect --bootstrap
```

# 构建镜像
构建并直接推送到指定仓库
```
docker buildx build -t nasus/etcd:3.5.0 --platform linux/amd64,linux/arm64 . --push
```
构建并导出到本地Docker images中
```
docker buildx build -t nasus/etcd:3.5.0 --platform linux/arm64 . --load
```
---
title: Hexo Docker部署
date: 2021-08-30 11:20:00
tags:
 - Hexo
 - Docker
---

#### 1. 下载node docker镜像

```bash
docker pull node:12-alpine
```

因目前node版本如果超过12则hexo部署会报错, 我们选择使用12版本

#### 2. 创建Hexo镜像

编写Dockerfile

```DOck
FROM node:12-alpine
RUN npm install -g hexo-cli
RUN apk add openssh
RUN apk add git
RUN git config --global user.email "kaychen.cn@gmail.com"
RUN git config --global user.name "kaychen"

WORKDIR /data
```

生产镜像

```bash
docker build -t hexo:12-alpine .
```

#### 3. 增加本地命令

在.zshrc中增加

```bash
hexo () {
    docker run \
        -it \
        --rm \
        -p 4000:4000 \
        --volume $PWD:/data:rw \
        --volume $HOME:/root:rw \
        --workdir /data \
        hexo:12-alpine hexo "$@"
}
```

让配置生效

```bash
source .zshrc
```

#### 4. 使用hexo

```bash
cd {项目目录}
hexo s -g //预览
hexo d -g //部署
```


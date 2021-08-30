---
title: Python Docker环境
date: 2021-08-31 18:00:00
tags:
  - Python
  - Docker
---

#### 拉取python镜像

```bash
docker pull python:3-alpine
```

#### 创建Dockerfile

安装自己需要的包

```dockerfile
FROM python:3-alpine
RUN pip install pandas
RUN pip install xlrd
RUN apk add build-base postgresql-dev
RUN pip install psycopg2

WORKDIR /data
```

#### Build镜像

```shell
docker build -t python3:alpine .
```

#### 启动测试

```bash
docker run -it --name python3 -v $PWD:/data python3:alpine sh
```

#### 主机使用python容器cli

在.zshrc中增加

```bash
python () {
    docker run \
        -it \
        --rm \
        --volume $PWD:/data:rw \
        --workdir /data \
        python3:alpine python "$@"
}
```

执行`source .zshrc`生效

#### 使用

执行`python -V`测试输出版本号即可


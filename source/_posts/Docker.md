---
title: Docker命令整理
date: 2017-10-01 13:08
tags: 
 - Docker
---

- Docker 容器镜像删除

<!--more-->

## 1.停止所有的container，这样才能够删除其中的images：

```shell
docker stop $(docker ps -a -q)
```

如果想要删除所有container的话再加一个指令：

```shell
docker rm $(docker ps -a -q)
```

## 2.查看当前有些什么images

```shell
docker images
```

## 3.删除images，通过image的id来指定删除谁

```shell
docker rmi <image id>
```

想要删除untagged images，也就是那些id为\<None\>的image的话可以用

```shell
docker rmi $(docker images | grep "^<none>" | awk "{print $3}")
```

要删除全部image的话

```shell
docker rmi $(docker images -q)
```

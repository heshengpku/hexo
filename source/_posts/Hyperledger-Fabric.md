---
category: Blockchain
tags: 
 - Blockchain
 - Hyperledger
 - Fabric
date: 2018-01-28 9:03
title: Fabric学习笔记
---

## 数据持久化

如果需要对peer或CouchDB容器的数据持久化，一种选择是将容器的相关目录挂载到docker主机。
例如，将下面两行内容放到docker-compose.yaml文件中的对应peer处：
```yaml
volumes:
  - /var/hyperledger/peer0:/var/hyperledger/production
```

将下面两行放到对应的CouchDB处：
```yaml
volumes:
  - /var/hyperledger/couchdb0:/opt/couchdb/data
```
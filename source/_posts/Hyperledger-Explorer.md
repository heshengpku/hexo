---
title: Hyperledger-Explorer
date: 2018-06-04 11:36:59
category: Blockchain
tags: 
 - Blockchain 
 - Hyperledger 
 - Fabric 
 - Explorer
---

Hyperledger项目提供了一个官方的区块链浏览器（现在只支持Fabric），见[官方文档](https://github.com/hyperledger/blockchain-explorer)。

Explorer项目最早使用的是MySQL的数据库，但现在又切换成了PostgresSQL。官方项目没有提供docker部署的方式，这里提供一种用docker-compose部署的方法，则可以不需要安装node和npm了。

<!--more-->

## git下载源码
```shell
git clone https://github.com/hyperledger/blockchain-explorer.git
cd blockchain-explorer
```

## 创建`Dockerfile`文件
```docker
FROM node:6.9.5

RUN mkdir -p /home/explorer/
WORKDIR /home/explorer/
COPY . /home/explorer/
VOLUME /home/explorer/crypto-config/
RUN npm install && cd /home/explorer/client/ && npm install && npm run build
CMD node /home/explorer/main.js
EXPOSE 8080
```

可以创建一个`.dockerignore`文件避免docker打包不必要的文件：
```
Dockerfile
.git/
docker-compose.yaml
```

这样可以将blockchain-explorer打包成docker镜像，注意修改`config.json`中的配置，也可以在启动容器是将`config.json`进行挂载。

```json
{
	"network-config": {
		"org1": {
			"name": "peerOrg1",
			"mspid": "Org1MSP",
			"peer1": {
				"requests": "grpcs://peer0.org1.example.com:7051",
				"events": "grpcs://peer0.org1.example.com:7053",
				"server-hostname": "peer0.org1.example.com",
				"tls_cacerts": "./crypto-config/peerOrganizations/org1.example.com/peers/peer0.org1.example.com/tls/ca.crt"
			},
			"peer2": {
				"requests": "grpcs://peer1.org1.example.com:7051",
				"events": "grpcs://peer1.org1.example.com:7053",
				"server-hostname": "peer1.org1.example.com",
				"tls_cacerts": "./crypto-config/peerOrganizations/org1.example.com/peers/peer1.org1.example.com/tls/ca.crt"
			},
			"admin": {
				"key": "./crypto-config/peerOrganizations/org1.example.com/users/Admin@org1.example.com/msp/keystore",
				"cert": "./crypto-config/peerOrganizations/org1.example.com/users/Admin@org1.example.com/msp/signcerts"
			}
		},
		"org2": {
			"name": "peerOrg2",
			"mspid": "Org2MSP",
			"peer1": {
				"requests": "grpcs://peer0.org2.example.com:7051",
				"events": "grpcs://peer0.org2.example.com:7053",
				"server-hostname": "peer0.org2.example.com",
				"tls_cacerts": "./crypto-config/peerOrganizations/org2.example.com/peers/peer0.org2.example.com/tls/ca.crt"
			},
			"peer2": {
				"requests": "grpcs://peer1.org2.example.com:7051",
				"events": "grpcs://peer1.org2.example.com:7053",
				"server-hostname": "peer1.org2.example.com",
				"tls_cacerts": "./crypto-config/peerOrganizations/org2.example.com/peers/peer1.org2.example.com/tls/ca.crt"
			},
			"admin": {
				"key": "./crypto-config/peerOrganizations/org2.example.com/users/Admin@org2.example.com/msp/keystore",
				"cert": "./crypto-config/peerOrganizations/org2.example.com/users/Admin@org2.example.com/msp/signcerts"
			}
		}
	},
	"host": "localhost",
	"port": "8080",
	"channel": "mychannel",
	"keyValueStore": "/tmp/fabric-client-kvs",
	"eventWaitTime": "30000",
	"users":[
		{
		   "username":"admin",
		   "secret":"adminpw"
		}
	 ],
	"pg": {
        "host":"db",
		"port": "5432",
		"database": "fabricexplorer",
		"username": "postgres",
		"passwd": "password"
	},
	"license": "Apache-2.0"
}
```

## 创建`docker-compose.yaml`文件
```yaml
# Copyright 2018 He Sheng. All Rights Reserved.
#

version: "2"

networks:
   byfn:

services:

  postgres:
     container_name: postgres
     image: postgres
     restart: always
     environment:
       - POSTGRES_PASSWORD=password
     volumes:
       - ./app/db/:/docker-entrypoint-initdb.d/
     ports:
       - "5432:5432"
     networks:
       - byfn


  explorer:
     build: .
     container_name: explorer
     volumes:
       - ${NETWORK}/crypto-config/:/home/explorer/crypto-config/
       - ./config.json:/home/explorer/config.json
     ports:
       - "8080:8080"
     networks:
       - byfn
     links:
       - postgres:db
```

其中
```yaml
     volumes:
       - ./app/db/:/docker-entrypoint-initdb.d/
```
可以使`postgres`容器启动后自动运行`app/db/`里的数据库文件`explorerpg.sql`和`updatepg.sql`。

注意修改`${NETWORK}`为部署Fabric网络对应的证书文件夹位置。

## 使用`docker-compose`部署
```shell
docker-compose -f docker-compose.yaml up
```

会自动拉取最新的`postgres`镜像和生成`explorer`的镜像。

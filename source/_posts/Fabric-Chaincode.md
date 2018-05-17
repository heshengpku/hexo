---
category: Blockchain
tags: 
 - Blockchain 
 - Hyperledger 
 - Fabric 
 - Chaincode
date: 2018-01-02 13:08
title: Fabric chaincode开发调试
---

链码（`Chaincode`）即`Fabric`中的智能合约，作为运行在`Fabric`区块链网络上的程序，链码在一般的运行模式下是运行在docker容器中的。
为了方便在开发中调试链码，可以使用开发模式。

- 需要已经安装：`docker`、`docker-compose`和`Fabric`的相关镜像，
`Go`和`Fabric`的源码不是必需的，因为调试仍然是在`Fabric`镜像中进行。

<!--more-->

### 1. 首先下载官方提供的fabric-samples：
```shell
git clone https://github.com/hyperledger/fabric-samples.git
```

将需要调试的chaincode源码（比如`gocc`）放入链码目录（`fabric-samples/chaincode`）

如果要使用couchdb作为状态数据库, 应该在`docker-comose-simple.yaml`的`peer`环境变量中添加：
```yaml
- CORE_LEDGER_STATE_STATEDATABASE=CouchDB
- CORE_LEDGER_STATE_COUCHDBCONFIG_COUCHDBADDRESS=couchdb:5984
```
在依赖中添加：
```yaml
depends_on:
  - orderer
  - couchdb
```
并添加couchdb容器：
```yaml
couchdb:
  container_name: couchdb
  image: hyperledger/fabric-couchdb
  ports:
    - 5984:5984
  environment:
    DB_URL: http://localhost:5984/member_db
```

为了使用`DEBUG`模式调试链码，需要在`chaincode`容器中添加环境变量：
```yaml
- CORE_CHAINCODE_LOGGING_LEVEL=DEBUG
```

#### 2. 打开终端1

启动网络：
```shell
cd fabric-samples/chaincode-docker-devmode
docker-compose -f docker-compose-simple.yaml up -d
```

进入链码容器：
```shell
docker exec -it chaincode bash
```

编译链码（假设链码文件夹为`/gocc`）
```shell
cd gocc
go build
```
启动链码：
```shell
CORE_PEER_ADDRESS=peer:7051 CORE_CHAINCODE_ID_NAME=mycc:0 ./gocc
```

#### 3. 打开终端2

进入fabric客户端：
```shell
docker exec -it cli bash
```
安装并实例化：
```shell
peer chaincode install -p chaincodedev/chaincode/sacc -n mycc -v 0
peer chaincode instantiate -n mycc -v 0 -c '{"Args":[""]}' -C myc
```
调用
```shell
peer chaincode invoke -n mycc -v 0 -c '{"Args":["..."]}' -C myc
```

查询
```shell
peer chaincode query -n mycc -v 0 -c '{"Args":["..."]}' -C myc
```

#### 4. 关闭网络

退出终端
```
exit
```

关闭网络：
```shell
docker-compose -f docker-compose-simple.yaml down
```

删除`myc.block`文件，下次才能正常启动网络:
```shell
rm myc.block
```


## Fabric + CouchDB 实现排序查询
在[localhost:5984/_utils](localhost:5984/_utils)可以打开`Fauxton`直接查看couchdb信息
为需要排序的字段创建索引文件（index），然后即可在网页客户端或者chaincode中的`rich query`中查询：
- 在`Design Documents`中添加`Mango Indexes`
- 创建索引文件，例如：

```json
{
    "index": {
        "fields": [{
            "data.createTime": "desc"
        }, {
            "chaincodeid": "desc"
        }, {
            "data.docType": "desc"
        }]
    },
    "ddoc": "indexTimeSortDoc",
    "name": "indexTimeSortDesc",
    "type": "json"
}
```
- 即为`createTime`字段创建了一个默认反序排列的索引，其中`chaincode`与`docType`必不可少，`ddoc`与`name`的具体值随意
- 在`Run A Query with Mango`中即可实现正反序查询：

```json
{
    "selector": {
        "data.docType": "typename"
    },
    "sort": [{
        "data.createTime": "desc"
    }, {
        "chaincodeid": "desc"
    }, {
        "data.docType": "desc"
    }]
}
```
- 注意不同的字段排序方法需要创建不同的索引文件

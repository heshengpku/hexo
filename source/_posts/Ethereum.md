---
category: Blockchain
tags: 
 - Ethereum
date: 2017-11-01 13:08
title: 以太坊私有链搭建操作指南
---

来源：https://my.oschina.net/u/2349981/blog/865256
作者：dophin459

虽然以太坊是一个公有链系统，但是我们可以通过设置一些参数来运行自己的私有链节点，在自己的私有链上进行开发和测试不需要同步公有链数据，也不需要花钱来买以太币，节省存储空间和成本，而且很灵活很方便。本文介绍使用`geth`客户端搭建私有链的操作步骤，同时会解释在这个过程中用到的各个命令及选项的含义和作用，最后会介绍`geth`的`Javascript Console`中的一些常用功能。

<!--more-->

## 系统和软件

- Ubuntu 16.04 64位
- geth 1.5.6 =>([安装教程](https://my.oschina.net/u/2349981/blog/856956))

## 搭建私有链

### 一、准备创世区块配置文件

以太坊支持自定义创世区块，要运行私有链，我们就需要定义自己的创世区块，创世区块信息写在一个json格式的配置文件中。首先将下面的内容保存到一个json文件中，例如`genesis.json`。

```json
{
    "coinbase"   : "0x0000000000000000000000000000000000000000",
    "difficulty" : "0x400",
    "extraData"  : "0x0",
    "gasLimit"   : "0x2fefd8",
    "nonce"      : "0xdeadbeefdeadbeef",
    "mixhash"    : "0x0000000000000000000000000000000000000000000000000000000000000000",
    "parentHash" : "0x0000000000000000000000000000000000000000000000000000000000000000",
    "timestamp"  : "0x00",
    "alloc"      : {}
}
```

### 二、初始化：写入创世区块

准备好创世区块配置文件后，需要初始化区块链，将上面的创世区块信息写入到区块链中。首先要新建一个目录用来存放区块链数据，假设新建的数据目录为`~/privatechain/data0`，`genesis.json`保存在`~/privatechain`中，此时目录结构应该是这样的：

```text
privatechain
├── data0
└── genesis.json
```

接下来进入`privatechain`中，执行初始化命令：

```shell
$ cd privatechain
$ geth --datadir data0 init genesis.json
```

上面的命令的主体是`geth init`，表示初始化区块链，命令可以带有选项和参数，其中`--datadir`选项后面跟一个目录名，这里为`data0`，表示指定数据存放目录为`data0`，`genesis.json`是`init`命令的参数。

运行上面的命令，会读取`genesis.json`文件，根据其中的内容，将创世区块写入到区块链中。如果看到以下的输出内容，说明初始化成功了。

```text
I0322 10:52:44.585282 cmd/geth/chaincmd.go:131] successfully wrote genesis block and/or chain rule set: b240e0678c2a8f87cf350225b528e3d97688aad6d4d84ee84e405c7fc9e37e4e
```

初始化成功后，会在数据目录`data0`中生成`geth`和`keystore`两个文件夹，此时目录结构如下：

```text
privatechain
├── data0
│   ├── geth
│   │   └── chaindata
│   │       ├── 000002.log
│   │       ├── CURRENT
│   │       ├── LOCK
│   │       ├── LOG
│   │       └── MANIFEST-000003
│   └── keystore
└── genesis.json
```

其中`geth/chaindata`中存放的是区块数据，`keystore`中存放的是账户数据。

#### 三、启动私有链节点

初始化完成后，就有了一条自己的私有链，之后就可以启动自己的私有链节点并做一些操作，在终端中输入以下命令即可启动节点：

```shell
$ geth --datadir data0 --networkid 1108 console
```

上面命令的主体是`geth console`，表示启动节点并进入交互式控制台，`--datadir`选项指定使用`data0`作为数据目录，`--networkid`选项后面跟一个数字，这里是`1108`，表示指定这个私有链的网络id为`1108`。网络id在连接到其他节点的时候会用到，以太坊公网的网络id是1，为了不与公有链网络冲突，运行私有链节点的时候要指定自己的网络id。

运行上面的命令后，就启动了区块链节点并进入了`Javascript Console`：

```shell
...
Welcome to the Geth JavaScript console!

instance: Geth/v1.5.6-stable/linux/go1.7.3
    modules: admin:1.0 debug:1.0 eth:1.0 miner:1.0 net:1.0 personal:1.0 rpc:1.0 txpool:1.0 web3:1.0

>
```

这是一个交互式的Javascript执行环境，在这里面可以执行Javascript代码，其中`>`是命令提示符。在这个环境里也内置了一些用来操作以太坊的Javascript对象，可以直接使用这些对象。这些对象主要包括：

```
eth：包含一些跟操作区块链相关的方法
net：包含以下查看p2p网络状态的方法
admin：包含一些与管理节点相关的方法
miner：包含启动&停止挖矿的一些方法
personal：主要包含一些管理账户的方法
txpool：包含一些查看交易内存池的方法
web3：包含了以上对象，还包含一些单位换算的方法
```

## 探索`Javascript Console`

进入以太坊`Javascript Console`后，就可以使用里面的内置对象做一些操作，这些内置对象提供的功能很丰富，比如查看区块和交易、创建账户、挖矿、发送交易、部署智能合约等。 接下来介绍几个常用功能，下面的操作中，前面带>的表示在`Javascript Console`中执行的命令。

### 一、创建账户

前面只是搭建了私有链，并没有自己的账户，可以在`js console`中输入`eth.accounts`来验证：

```shell
> eth.accounts
[]
```

接下来使用`personal`对象来创建一个账户：

```shell
> personal.newAccount()
Passphrase:
Repeat passphrase:
"0xc232e2add308136571bb8f9197ba4ae4e5ba9836"
```

会提示输入密码和确认密码，输入密码不会有显示，只要输入就可以了，之后就会显示新创建的账户地址。

可以创建多个账户，我们再来创建一个账户：

```shell
> personal.newAccount()
Passphrase:
Repeat passphrase:
"0x814d39aa21f3eed069f2b21da7b5f857f7343afa"
```

接下来就可以查看到刚才创建的两个账户了：

```shell
> eth.accounts
["0xc232e2add308136571bb8f9197ba4ae4e5ba9836", "0x814d39aa21f3eed069f2b21da7b5f857f7343afa"]
```

账户默认会保存在数据目录的`keystore`文件夹中。查看目录结构，发现`data0/keystore`中多了两个文件，这两个文件就对应刚才创建的两个账户，这是json格式的文本文件，可以打开查看，里面存的是私钥经过密码加密后的信息。

```text
data0
├── geth
│   ├── chaindata
│   ├── LOCK
│   ├── nodekey
│   └── nodes
├── geth.ipc
├── history
└── keystore
    ├── UTC--2017-03-22T09-06-47.766993033Z--c232e2add308136571bb8f9197ba4ae4e5ba9836
    └── UTC--2017-03-22T09-09-42.041015656Z--814d39aa21f3eed069f2b21da7b5f857f7343afa
```

小提示：命令都可以按`Tab`键自动补全。

### 二、查看账户余额

eth对象提供了查看账户余额的方法：

```shell
> eth.getBalance(eth.accounts[0])
0
> eth.getBalance(eth.accounts[1])
0
```

目前两个账户的以太币余额都是0，要使账户有余额，可以从其他账户转账过来，或者通过挖矿来获得以太币奖励。

#### 三、启动&停止挖矿

通过`miner.start()`来启动挖矿：

```shell
> miner.start(1)
```

其中`start`的参数表示挖矿使用的线程数。第一次启动挖矿会先生成挖矿所需的DAG文件，这个过程有点慢，等进度达到100%后，就会开始挖矿，此时屏幕会被挖矿信息刷屏。

如果想停止挖矿，在`js console`中输入`miner.stop()`：

```shell
> miner.stop()
```

注意：输入的字符会被挖矿刷屏信息冲掉，没有关系，只要输入完整的`miner.stop()`之后回车，即可停止挖矿。

挖到一个区块会奖励5个以太币，挖矿所得的奖励会进入矿工的账户，这个账户叫做coinbase，默认情况下coinbase是本地账户中的第一个账户：

```shell
> eth.coinbase
"0xc232e2add308136571bb8f9197ba4ae4e5ba9836"
```

现在的coinbase是账户0，要想使挖矿奖励进入其他账户，通过`miner.setEtherbase()`将其他账户设置成coinbase即可：

```shell
> miner.setEtherbase(eth.accounts[1])
true
> eth.coinbase
"0x814d39aa21f3eed069f2b21da7b5f857f7343afa"
```

我们还是以账户0作为coinbase，挖到区块以后，账户0里面应该就有余额了：

```shell
> eth.getBalance(eth.accounts[0])
160000000000000000000
```

`getBalance()`返回值的单位是wei，wei是以太币的最小单位，1个以太币=10的18次方个wei。要查看有多少个以太币，可以用`web3.fromWei()`将返回值换算成以太币：

```shell
> web3.fromWei(eth.getBalance(eth.accounts[0]),'ether')
160
```

### 四、发送交易

目前，账户一的余额还是0：

```shell
> eth.getBalance(eth.accounts[1])
0
```

可以通过发送一笔交易，从账户0转移5个以太币到账户1：

```shell
> amount = web3.toWei(5,'ether')
"5000000000000000000"
> eth.sendTransaction({from:eth.accounts[0],to:eth.accounts[1],value:amount})
Error: account is locked
    at web3.js:3119:20
    at web3.js:6023:15
    at web3.js:4995:36
    at <anonymous>:1:1
```

这里报错了，原因是账户每隔一段时间就会被锁住，要发送交易，必须先解锁账户，由于我们要从账户0发送交易，所以要解锁账户0：

```shell
> personal.unlockAccount(eth.accounts[0])
Unlock account 0xc232e2add308136571bb8f9197ba4ae4e5ba9836
Passphrase:
true
```

输入创建账户时设置的密码，就可以成功解锁账户。然后再发送交易：

```shell
> amount = web3.toWei(5,'ether')
"5000000000000000000"
> eth.sendTransaction({from:eth.accounts[0],to:eth.accounts[1],value:amount})
I0322 19:39:36.300675 internal/ethapi/api.go:1047] Tx(0x0c59f431068937cbe9e230483bc79f59bd7146edc8ff5ec37fea6710adcab825) to: 0x814d39aa21f3eed069f2b21da7b5f857f7343afa
"0x0c59f431068937cbe9e230483bc79f59bd7146edc8ff5ec37fea6710adcab825"
```

此时交易已经提交到区块链，返回了交易的hash，但还未被处理，这可以通过查看`txpool`来验证：

```shell
> txpool.status
{
    pending: 1,
    queued: 0
}
```

其中有一条pending的交易，pending表示已提交但还未被处理的交易。

要使交易被处理，必须要挖矿。这里我们启动挖矿，然后等待挖到一个区块之后就停止挖矿：

```shell
> miner.start(1);admin.sleepBlocks(1);miner.stop();
```

当`miner.stop()`返回true后，`txpool`中pending的交易数量应该为0了，说明交易已经被处理了：

```shell
> txpool.status
{
    pending: 0,
    queued: 0
}
```

此时，交易已经生效，账户一应该已经收到了5个以太币了：

```shell
> web3.fromWei(eth.getBalance(eth.accounts[1]),'ether')
5
```

### 五、查看交易和区块

`eth`对象封装了查看交易和区块信息的方法。

查看当前区块总数：

```shell
> eth.blockNumber
33
```

通过交易hash查看交易：

```shell
> eth.getTransaction("0x0c59f431068937cbe9e230483bc79f59bd7146edc8ff5ec37fea6710adcab825")
{
    blockHash: "0xf5d3da50065ce5793c9571a031ad6fe5f1af326a3c4fb7ce16458f4d909c1613",
    blockNumber: 33,
    from: "0xc232e2add308136571bb8f9197ba4ae4e5ba9836",
    gas: 90000,
    gasPrice: 20000000000,
    hash: "0x0c59f431068937cbe9e230483bc79f59bd7146edc8ff5ec37fea6710adcab825",
    input: "0x",
    nonce: 0,
    r: "0x433fe5845391b6da3d8aa0d2b53674e09fb6126f0070a600686809b57e4ef77d",
    s: "0x6b0086fb76c46024f849141074a5bc79c49d5f9a658fd0fedbbe354889c34d8d",
    to: "0x814d39aa21f3eed069f2b21da7b5f857f7343afa",
    transactionIndex: 0,
    v: "0x1b",
    value: 5000000000000000000
}
```

通过区块号查看区块：

```shell
> eth.getBlock(33)
{
    difficulty: 132928,
    extraData: "0xd783010506846765746887676f312e372e33856c696e7578",
    gasLimit: 3244382,
    gasUsed: 21000,
    hash: "0xf5d3da50065ce5793c9571a031ad6fe5f1af326a3c4fb7ce16458f4d909c1613",
    logsBloom: "0x00000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000",
    miner: "0xc232e2add308136571bb8f9197ba4ae4e5ba9836",
    mixHash: "0x09849dff7c8b8467812fa80d1fa2a27bc61f1cf16d5b2c05a6ce1b77ee18f3f1",
    nonce: "0x5b3939449dbdbea0",
    number: 33,
    parentHash: "0xeca34637642f56f7cfe5b699031c7ddbc43aee00fb10c7f054e0a9719cf226da",
    receiptsRoot: "0xd5f5b7ee944e57cbff496f7bdda7ceffd5eedffe6d5be5320008190502adc07a",
    sha3Uncles: "0x1dcc4de8dec75d7aab85b567b6ccd41ad312451b948a7413f0a142fd40d49347",
    size: 649,
    stateRoot: "0xc7add6b756980ab9e482766e455597ef1583e747ad62e2924a8e66c6f9170112",
    timestamp: 1490183209,
    totalDifficulty: 4358016,
    transactions: ["0x0c59f431068937cbe9e230483bc79f59bd7146edc8ff5ec37fea6710adcab825"],
    transactionsRoot: "0x7335a362b2c3925e7ba1b41bf7772aa9645a13d4f9c12edd5892b87887264232",
    uncles: []EthereumEthereum
}
```

还有更多的功能请自行探索...。

### 六、连接到其他节点

可以通过`admin.addPeer()`方法连接到其他节点，两个节点要想联通，必须保证网络是相通的，并且要指定相同的networkid。

假设有两个节点：节点一和节点二，networkid都是1108，通过下面的步骤就可以从节点一连接到节点二。

首先要知道节点二的enode信息，在节点二的`js console`中执行下面的命令查看enode信息：

```shell
> admin.nodeInfo.enode
"enode://9e86289ea859ca041f235aed87a091d0cd594b377cbe13e1c5f5a08a8a280e62d4019ac54063ed6a1d0e3c3eaedad0b73c40b99a16a176993f0373ffe92be672@[::]:30304"
```

然后在节点一的`js console`中执行`admin.addPeer()`，就可以连接到节点二：

```shell
> admin.addPeer("enode://9e86289ea859ca041f235aed87a091d0cd594b377cbe13e1c5f5a08a8a280e62d4019ac54063ed6a1d0e3c3eaedad0b73c40b99a16a176993f0373ffe92be672@127.0.0.1:30304")
```

`addPeer()`的参数就是节点二的enode信息，注意要把enode中的`[::]`替换成节点二的IP地址。连接成功后，节点二就会开始同步节点一的区块，同步完成后，任意一个节点开始挖矿，另一个节点会自动同步区块，向任意一个节点发送交易，另一个节点也会收到该笔交易。

通过`admin.peers`可以查看连接到的其他节点信息，通过`net.peerCount`可以查看已连接到的节点数量。

除了上面的方法，也可以在启动节点的时候指定`--bootnodes`选项连接到其他节点。

## [以太坊源码剖析：交易](https://bitshuo.com/topic/5948897c8e822fcb444317d0)

交易是区块链中最基本也是最核心的一个概念，在以太坊中，交易更是重中之重，因为以太坊是一个智能合约平台，以太坊上的应用都是通过智能合约与区块链进行交互，而智能合约的执行是由交易触发的，没有交易，智能合约就是一段死的代码，可以说在以太坊中，一切都源于交易。下面就来看看在以太坊中交易是什么样的，交易里面都有什么。
交易的数据结构

在core/types/transaction.go中定义了交易的数据结构：

```go
type Transaction struct {
    data txdata
    // caches
    hash atomic.Value
    size atomic.Value
    from atomic.Value
}
```

在这个结构体里面只有一个data字段，它是txdata类型的，其他的三个字段hash size from是缓存字段，txdata也是一个结构体，它里面定义了交易的具体的字段：

```go
type txdata struct {
    AccountNonce    uint64
    Price, GasLimit *big.Int
    Recipient       *common.Address `rlp:"nil"` // nil means contract creation
    Amount          *big.Int
    Payload         []byte
    V               *big.Int // signature
    R, S            *big.Int // signature
}
```

各字段的含义如下：

```text
AccountNonce：此交易的发送者已发送过的交易数
Price：此交易的gas price
GasLimit：本交易允许消耗的最大gas数量
Recipient：交易的接收者，是一个地址
Amount：交易转移的以太币数量，单位是wei
Payload：交易可以携带的数据，在不同类型的交易中有不同的含义
V R S：交易的签名数据
```

注意：这里并没有一个字段来指明交易的发送者，因为交易的发送者地址可以从签名中得到。

在transaction.go中还定义了一个jsonTransaction结构体，这个结构体用于将交易进行json序列化和反序列化，具体的序列化和反序列化可以参照MarshalJSON和UnmarshalJSON函数。以太坊节点会向外部提供JSON RPC服务，供外部调用，RPC服务通过json格式传输数据，节点收到json数据后，会转换成内部的数据结构来使用。jsonTransaction结构体使用go语言的struct tag特性指定了内部数据结构与json数据各字段的对应关系，例如内部的AccountNonce对应json的nonce，Amount对应json的value。web3.js的eth.getTransaction()和eth.sendTransaction()使用的数据就是json格式的，根据这个结构体就可以知道在web3.js中交易的各个字段与程序内部的各个字段的对应关系。

```go
type jsonTransaction struct {
    Hash         *common.Hash    `json:"hash"`
    AccountNonce *hexutil.Uint64 `json:"nonce"`
    Price        *hexutil.Big    `json:"gasPrice"`
    GasLimit     *hexutil.Big    `json:"gas"`
    Recipient    *common.Address `json:"to"`
    Amount       *hexutil.Big    `json:"value"`
    Payload      *hexutil.Bytes  `json:"input"`
    V            *hexutil.Big    `json:"v"`
    R            *hexutil.Big    `json:"r"`
    S            *hexutil.Big    `json:"s"`
}
```

注：Payload这个字段在eth.sendTransaction()中对应的是data字段，在eth.getTransaction()中对应的是input字段。
交易的Hash

下面是计算交易Hash的函数，它是先从缓存tx.hash中取，如果取到，就直接返回，如果缓存中没有，就调用rlpHash计算hash，然后把hash值加入到缓存中。

```go
// Hash hashes the RLP encoding of tx.
// It uniquely identifies the transaction.
func (tx *Transaction) Hash() common.Hash {
    if hash := tx.hash.Load(); hash != nil {
        return hash.(common.Hash)
    }
    v := rlpHash(tx)
    tx.hash.Store(v)
    return v
}
```

rlpHash的代码在core/types/block.go中：

```go
func rlpHash(x interface{}) (h common.Hash) {
    hw := sha3.NewKeccak256()
    rlp.Encode(hw, x)
    hw.Sum(h[:0])
    return h
}
```

从rlpHash函数可以看出，计算hash的方法是先对交易进行RLP编码，然后计算RLP编码数据的hash，具体的hash算法是Keccak256。

那么到底是对交易中的哪些字段计算的hash呢？这就要看rlp.Encode对哪些字段进行了编码。rlp.Encode代码在rlp/encode.go中，不用看具体的实现，在注释中有这么一段：

```go
// If the type implements the Encoder interface, Encode calls
// EncodeRLP. This is true even for nil pointers, please see the
// documentation for Encoder.
```

就是说如果一个类型实现了Encoder接口，那么Encode函数就会调用那个类型所实现的EncodeRLP函数。所以我们就要看Transaction这个结构体是否实现了EncodeRLP函数。回到core/types/transaction.go中，可以看到Transaction确实实现了EncodeRLP函数：

```go
// DecodeRLP implements rlp.Encoder
func (tx *Transaction) EncodeRLP(w io.Writer) error {
    return rlp.Encode(w, &tx.data)
}
```

从这可以看出交易的hash实际上是对tx.data进行hash计算得到的：txhash=Keccak256(rlpEncode(tx.data))。
交易的类型

在源码中交易只有一种数据结构，如果非要给交易分个类的话，我认为交易可以分为三种：转账的交易、创建合约的交易、执行合约的交易。web3.js提供了发送交易的接口：

```js
web3.eth.sendTransaction(transactionObject [, callback])
```

参数是一个对象，在发送交易的时候指定不同的字段，区块链节点就可以识别出对应类型的交易。
转账的交易

转账是最简单的一种交易，这里转账是指从一个账户向另一个账户发送以太币。发送转账交易的时候只需要指定交易的发送者、接收者、转币的数量。使用web3.js发送转账交易应该像这样：

```js
web3.eth.sendTransaction({
    from: "0xb60e8dd61c5d32be8058bb8eb970870f07233155",
    to: "0xd46e8dd67c5d32be8058bb8eb970870f07244567",
    value: 10000000000000000
});
```

value是转移的以太币数量，单位是wei，对应的是源码中的Amount字段。to对应的是源码中的Recipient。
创建合约的交易

创建合约指的是将合约部署到区块链上，这也是通过发送交易来实现。在创建合约的交易中，to字段要留空不填，在data字段中指定合约的二进制代码，from字段是交易的发送者也是合约的创建者。

```js
web3.eth.sendTransaction({
    from: "contract creator's address",
    data: "contract binary code"
});
```

data字段对应的是源码中的Payload字段。
执行合约的交易

调用合约中的方法，需要将交易的to字段指定为要调用的合约的地址，通过data字段指定要调用的方法以及向该方法传递的参数。

```js
web3.eth.sendTransaction({
    from: "sender's address",
    to: "contract address",
    data: "hash of the invoked method signature and encoded parameters"
});
```

data字段需要特殊的编码规则，具体细节可以参考Ethereum Contract ABI。自己拼接字段既不方便又容易出错，所以一般都使用封装好的SDK（比如web3.js）来调用合约。

```text
代码版本：1.5.9
```

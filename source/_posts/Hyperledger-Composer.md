---
category: Blockchain
tags: 
 - Blockchain 
 - Hyperledger 
 - Fabric 
 - Composer
date: 2017-10-11 13:08
title: Hyperledger Composer安装
---

由linux基金会主导的超级账本（`HyperLeger Fabric`)试图为新一代的事务应用创建一种开放的分布式账本标准，支持许可式区块链。

`Fabric`的开发环境建立在`VirtualBox`虚拟机上，部署环境可以自建网络，也可以直接部署在`BlueMix`上，部署方式可`docker`化，支持用`Go`和`JavaScript`开发智能合约。它采用`PBFT`分布式算法，网络编程方面用gRPC来做`P2P`通讯，使用 Protocol Buffer来序列化要传递的数据结构。在架构设计上，`Fabric`可能与比特币等区块链产品有所不同，但是上述基本组成部分还是不可或缺的。
在多核时代，`Go`语言备受喜爱，它可以让你用同步方式轻松实现高并发，特别是在分布式系统、网络编程等领域，应用非常广。所以，在区块链开发领域，也有很多使用`Go`语言的项目。

<!--more-->

## [Hyperledger][1]

[GitHub仓库][2]中的 [Fabric源码][3]

[Hyperledger Composer][4]

 - [Tutorials][5]
 - [Docs][6]
 - [Community][7]

### Hyperledger Fabric V1.0环境搭建
流程：WIN主机 -> VBox -> Ubuntu -> Docker -> Fabric Example

准备：VirtualBox 5.1.8、Ubuntu 16.04 LTS、Git 2.12.2、Go1.8、Docker CE17、Pip2

步骤：

#### 1. 安装 VirtualBox
官方下载安装包“[VirtualBox-5.1.18-114002-Win.exe][8]”，双击运行按默认方式安装即可。或者在Win7安装[Docker ToolBox][9]自带有Oracle VM VirtualBox。
启动VirtualBox，由于新建虚拟机占用空间较大，调整默认存储位置，如下设置：Vbox>管理>全局设定

![Vbox>管理>全局设定][10]

#### 2. 安装 Ubuntu
官方下载Ubuntu桌面版IOS镜像“[ubuntu-16.04.2-desktop-amd64.iso][11]”，进入VBox点击新建按钮，弹出窗口名称输入“hyperledger”，类型选择“Linux”版本选择“Ubuntu (64-bit)；继续下一步，内存大小输入4096MB，默认方式点击下一步，创建，下一步，下一步，虚拟硬盘输入30GB然后点击创建，虚拟机配置基本设置好了。接下来设置光盘启动安装系统，回到Vbox主界面选中刚新建的“hyperledger”项目后点击设置，界面如下：

![设置光盘启动安装系统][12]

选择磁盘弹出窗口选择刚下载好的ISO镜像，点击OK回到主界面点击启动，接下来就是Ubuntu系统安装过程了，安装过程按提示安装即可。最后进入Ubuntu系统安装增强工具（分辨率自动适应窗口大小），如下：

![进入Ubuntu系统安装增强工具][13]

Vbox增强工具安装好重启系统，接下来工作主要在是Terminal下操作，建议启用root用户来安装后续工具软件。（sudo passwd更改root密码，后续直接su就可切换至root用户了）

#### 3. 安装配套软件
`Hyperledger`的[开发工具需要][14]：
```shell
Operating Systems: Ubuntu Linux 14.04 / 16.04 LTS (both 64-bit), or Mac OS 10.12
Docker Engine: Version 17.03 or higher
Docker-Compose: Version 1.8 or higher
Node: 6.x (note version 7 is not supported)
npm: v3.x or v5.v
git: 2.9.x or higher
Python: 2.7.x
A code editor of your choice, we recommend VSCode.
```
但在Ubuntu上直接下载如下命令行脚本并运行（需要root权限）就可以了：
```shell
curl -O https://hyperledger.github.io/composer/prereqs-ubuntu.sh
chmod u+x prereqs-ubuntu.sh
./prereqs-ubuntu.sh
```
如果没有`curl`命令或者命令报错需先安装`curl`
```shell
apt-get update
apt-get install curl
```
在安装完成后**必须退出用户再重新登录**，重启Terminal后`npm`会自动更新，如果更新不成功需按提示赋予权限后再次重启用户。这样不会再出现`npm`和`node`无法运行的情况。

<!-- 在我第一次安装时发现`Node`没有正确安装，手动安装如下：

    apt-get install nodejs-legacy

但版本不够新（只有v4.2.6），所以先升级`npm`到最新版本

    apt-get install npm@latest -g

安装用于安装`nodejs`的模块`n`

    npm install -g n

通过`n`模块安装指定版本的`nodejs`(2017年8月23日：`Hyperledger` 还不支持 `Node` v7)

    n latest //安装官方最新版本
    n stable //安装官方稳定版本
    n lts    //安装官方最新LTS版本

当用户账号中`node`版本不合要求时，用

    which node

查看`node`链接位置，再在root下修改链接到需要的版本

    ln -s 版本位置 node位置

-->

运行Hyperledger Composer和Hyperledger Fabric推荐至少4Gb的内存

#### 4. 安装Hyperledger开发工具
一定要在non-privileged用户（eg non-root）下安装如下开发工具
```shell
npm install -g composer-cli
npm install -g generator-hyperledger-composer
npm install -g composer-rest-server
npm install -g yo
```
在安装中因为版本太旧弹出`npm WARN`可以不用理会

如出现权限错误：
```
npm ERR!    path: ...
...
```
可以通过在root中用`chown -R`命令赋予用户相关路径的权限来解决：
```shell
chown -R UserName:UserName Path
```
如果只运行本地版Playground，运行
```shell
npm install -g composer-playground
```
运行Hyperledger Fabric前需清空本地版Playground
```shell
docker kill $(docker ps -q)
docker rm $(docker ps -aq)
docker rmi $(docker images dev-* -q)
```
在文件夹（比如`~/fabric-tools`）安装zip文件
```shell
mkdir ~/fabric-tools && cd ~/fabric-tools

curl -O https://raw.githubusercontent.com/hyperledger/composer-tools/master/packages/fabric-dev-servers/fabric-dev-servers.zip
unzip fabric-dev-servers.zip
```
推荐并且默认运行**Hyperledger Fabric v1.0**版本，如果需要可以显式指定
```shell
export FABRIC_VERSION=hlfv1
```
所有脚本都在文件夹`~/fabric-tools`中。一个典型的Hyperledger Composer使用流程是
```shell
cd ~/fabric-tools
./downloadFabric.sh
./startFabric.sh
./createComposerProfile.sh
```
在用户下运行`./downloadFabric.sh`时可能出现`Docker permission denied`的错误，可以在`root`下运行
```shell
usermod -a -G docker UserName
```
或在用户进程下通过`sudo`命令解决
```shell
sudo usermod -a -G docker $USER
```
在此之后需要完全退出本用户并重新登录

在开发session结束后
```shell
cd ~/fabric-tools
./stopFabric.sh
./teardownFabric.sh
```
#### 5. 新建Hyperledger Composer Solution
在安装好Hyperledger Composer以后，就可以按[Developer Guide][15]商业网络定义（business network definition，BND）。BND依次定义了区块链方案的数据模型（data model）、商业和交易逻辑（business and therein transaction logic）、权限控制规则（access control rules）。

在非root用户下clone一个sample网络：
```shell
git clone https://github.com/hyperledger/composer-sample-networks.git
cp -r ./composer-sample-networks/packages/basic-sample-network/  ./my-network
```
为了方便可以下载[VSCode editor](https://code.visualstudio.com/)。安装后打开`VSCode`选择`View > Command Palette...`输入`extensions`，选择`Extensions:Install Extensions`选项。在`Search Extensions in Marketplace`输入`Composer`并安装`Hyperledger Composer extension`。

根据实例，依次新建project（`my-network`），更新`package.json`文件，更新`README.md`(说明文档)，定义主模型`models/sample.cto`（可以包括多个文件），编写交易函数`lib/sample.js`，更新权限控制规则`permissions.acl`。

生成商业网络档案（Business Network Archive）
```shell
cd my-network
npm install
```
`composer archive create`命令就在`dist`文件夹中创建了一个文件`my-network.bna`

编写Unit Tests(测试)，写入`test/sample.js`文件。然后编写文件`features/sample.feature`（测试定义文件）。再确认Unit测试通过：
```shell
npm test
```
导入Playground并测试。

运行composer-playground的快速方法：

1. 拉取composer-playground镜像
```shell
docker pull hyperledger/composer-playground
```
2. 本地启动镜像
```shell
docker run --name composer-playground -p 8080:8080 -d hyperledger/composer-playground
```
然后就可以在浏览器上使用`Hyperledger Composer Playground` http://localhost:8080/
3. 结束并关闭容器
```shell
docker rm -f composer-playground
```

#### 6. 部署到运行中`Hyperledger Fabric`

<!-- 在部署前确认`composer`正常，用`apt-get install composer`命令下载的`composer`版本较低，手动安装如下：

    php -r "copy('https://getcomposer.org/installer', 'composer-setup.php');"
    php composer-setup.php --install-dir=usr/bin --filename=composer --version=1.5.1 --quiet

如果出现错误Failed to decode zlib stream，安装`zlibc`

    apt-get install zlibc

错误理解，此composer非彼composer  -->

可以先试运行`composer`命令
```shell
composer
```
确认为`Hyperledger Composer`。如果命令不存在，可能是环境变量问题
```shell
export PATH = $PATH:/安装了composer-cli等工具的路径
```
将BNA部署到Hyperledger Fabric v1.0上
```shell
cd dist
composer network deploy -a my-network.bna -p hlfv1 -i PeerAdmin -s randomString
```
确认网络已经部署
```shell
composer network ping -n my-network -p hlfv1 -i admin -s adminpw
```
#### 7. 生成并测试REST API
登录服务器，在`my-network`文件夹中输入
```shell
composer-rest-server
```
按如下依次回答问题并设置

![REST API generated][16]{:height="100%" width="100%"}

如果运行成功，可以在输出中看到两行网络地址
```shell
Web server listening at: http://localhost:3000
Browse your REST API at http://localhost:3000/explorer
```
打开浏览器并输入`http://localhost:3000/explorer`就能看到

![LoopBack API Explorer][17]

包括`Commodity`，`System`，`Trade`,`Trader`

#### 8. 生成Skeleton Web Application

关闭`composer-rest-server`进程，运行
```shell
yo hyperledger-composer
```
可以看到

![yo][18]

等待几分钟到应用安装完成，进入新应用目录
```shell
cd my-app
npm start
```
可以看到`composer-rest-server`开始运行，Angular打包的网络应用在`http://localhost:4200`，点击Assets就可以看到存储在Hyperledger Fabric上的Commodity了。


  [1]: https://www.hyperledger.org/
  [2]: https://github.com/hyperledger/hyperledger
  [3]: https://github.com/hyperledger/fabric
  [4]: https://hyperledger.github.io/composer/index.html
  [5]: https://hyperledger.github.io/composer/tutorials/tutorials.html
  [6]: https://hyperledger.github.io/composer/introduction/introduction.html
  [7]: https://hyperledger.github.io/composer/support/support-index.html
  [8]: http://download.virtualbox.org/virtualbox/5.1.18/VirtualBox-5.1.18-114002-Win.exe
  [9]: https://www.docker.com/products/docker-toolbox
  [10]: https://sfault-image.b0.upaiyun.com/294/543/2945435109-58f0519cd37f3_articlex
  [11]: http://releases.ubuntu.com/16.04.2/ubuntu-16.04.2-desktop-amd64.iso
  [12]: https://sfault-image.b0.upaiyun.com/232/484/232484422-58f051ecde40f_articlex
  [13]: https://sfault-image.b0.upaiyun.com/119/447/1194474056-58f0521aaea82_articlex
  [14]: https://hyperledger.github.io/composer/installing/development-tools.html
  [15]: https://hyperledger.github.io/composer/tutorials/developer-guide.html
  [16]: https://hyperledger.github.io/composer/assets/img/tutorials/developer/composer-rest-server.png
  [17]: https://hyperledger.github.io/composer/assets/img/tutorials/developer/lb_explorer.png
  [18]: https://hyperledger.github.io/composer/assets/img/tutorials/developer/composer-yo-generator.png

---
category: Tools
title: SonarQube代码扫描
date: 2021-01-22 13:24:14
tags:
 - Golang
---

## 1、SonarQube容器化安装

采用docker-compose方式部署SonarQube服务，但是需要先做一些系统设置才能启动

- 对sonarqube挂载目录赋权：

```shell
cd /opt/sonarqube/   #与docker-compose.yml文件一致
chown -R 999:999 .
```

<!--more-->

- 由于 Elasticsearch 占用内存较高。官方要求 `max_map_count` 需要配置到最小 `262144`

```shell
sysctl -w vm.max_map_count=262144
```

看有的文章提到还需要设置 `fs.file-max`，但本地部署未设置没有发现问题

```shell
sysctl -w fs.file-max=65536
```

上述设置是临时生效，如果需要永久生效，需要编辑 `/etc/sysctl.conf` 文件加入以下配置

```shell
vm.max_map_count=262144
fs.file-max=65536
```

如果采用永久配置，保险起见，重启系统

- 创建docker-compose.yml文件如下

```yaml
version: '3'

services:
  postgres:
    image: postgres:12
    restart: always
    container_name: postgres
    ports:
      - 5432:5432
    volumes:
      - /opt/postgres/postgresql/:/var/lib/postgresql
      - /opt/postgres/data/:/var/lib/postgresql/data
    environment:
      TZ: Asia/Shanghai
      POSTGRES_USER: sonar
      POSTGRES_PASSWORD: sonar
      POSTGRES_DB: sonar

  sonar:
    image: sonarqube:7.9-community
    container_name: sonar
    depends_on:
      - postgres
    volumes:
      - /opt/sonarqube/extensions:/opt/sonarqube/extensions
      - /opt/sonarqube/logs:/opt/sonarqube/logs
      - /opt/sonarqube/data:/opt/sonarqube/data
      - /opt/sonarqube/conf:/opt/sonarqube/conf
    ports:
      - 9000:9000
    environment:
      SONARQUBE_JDBC_USERNAME: sonar
      SONARQUBE_JDBC_PASSWORD: sonar
      SONARQUBE_JDBC_URL: jdbc:postgresql://postgres:5432/sonar
```

- 启动

```shell
docker-compose up -d
```

在服务器部署时遇到了隔一段时间sonar容器宕机问题，排查可能还是es的jvm设置问题（内存不足导致堆栈溢出）。所以需要在部署文件中sonar的 `environment` 增加一项

```yaml
      SONARQUBE_SEARCH_JAVAOPTS="-Xmx1G -Xms1G" #对于运行ElasticSearch的JVM
```

如果出现类似的问题，可能需要增加其他jvm设置项

```yaml
      SONARQUBE_WEB_JAVAOPTS="-Xmx1G -Xms1G" #对于Web Server JVM
      SONARQUBE_CE_JAVAOPTS="-Xmx1G -Xms1G" #对于计算引擎JVM
```

1G的大小可以根据实际情况调整，一般不少于512m

## 2、配置代码项目的 `sonar-project.properties` 文件

需要安装 `sonar-scanner`

### go语言项目

- 在 SonarQube > Administration > Marketplace 中安装 `SonarGo` 插件

- 采用 `golangci-lint` 扫描，安装后运行

```shell
golangci-lint run -v --out-format checkstyle > report.xml
```

- `go test`执行单元测试，并输出覆盖率报告

```shell
go test -coverprofile=coverage.out ./...
go test -json ./... > report.json
```

- `sonar-project.properties` 示例如下：

```shell
sonar.host.url=http://localhost:9000
sonar.sourceEncoding=UTF-8
sonar.login=admin
sonar.password=admin
sonar.projectKey=<Progect_Name>
sonar.projectName=<Progect_Name>
sonar.projectVersion=1.0
sonar.go.golangci-lint.reportPaths=report.xml
sonar.go.coverage.reportPaths=coverage.out
sonar.go.tests.reportPaths=report.json
sonar.sources=./
sonar.exclusions=**/*_test.go,**/vendor/**
sonar.language=go
sonar.tests=.
sonar.test.inclusions=**/*_test.go
sonar.test.exclusions=**/vendor/**
```

- 运行 `sonar-scanner` 即可

### Java项目

- 在 SonarQube > Administration > Marketplace 中安装 `Java Code Quality and Security` 插件

- Sonarqube扫描Java项目需要先对项目编译，以maven为例

```shell
mvn compile
```

- `sonar-project.properties` 示例如下：

```shell
sonar.host.url=http://localhost:9000
sonar.sourceEncoding=UTF-8
sonar.login=admin
sonar.password=admin
sonar.projectKey=<Progect_Name>
sonar.projectName=<Progect_Name>
sonar.projectVersion=1.0
sonar.java.binaries=target/classes
sonar.sources=src
sonar.language=java
```

- 运行 `sonar-scanner` 即可

### JavaScript项目

- 在 SonarQube > Administration > Marketplace 中安装 `SonarJS` 插件，会自动安装 `SonarTS` 插件

- `sonar-project.properties` 示例如下：

```shell
sonar.host.url=http://localhost:9000
sonar.sourceEncoding=UTF-8
sonar.login=admin
sonar.password=admin
sonar.projectKey=<Progect_Name>
sonar.projectName=<Progect_Name>
sonar.projectVersion=1.0
sonar.sources=src
# sonar.tests=test
sonar.language=js
sonar.profile=node

sonar.dynamicAnalysis=reuseReports

sonar.javascript.jstest.reportsPath=reports
sonar.javascript.lcov.reportPath=reports/coverage.lcov
```

- 运行 `sonar-scanner` 即可

### Rust项目

- 在 SonarQube Marketplace 还没有官方 `Rust` 插件，需要手动将 [`SonarRust`](https://github.com/elegoff/sonar-rust) 插件安装到 Sonarqube 的 `extensions/plugins` 目录下，并重启 Sonarqube

- 采用 `cargo clippy` 扫描，运行

```shell
cargo clippy --message-format=json &> report.json
```

- `sonar-project.properties` 示例如下：

```shell
sonar.host.url=http://localhost:9000
sonar.sourceEncoding=UTF-8
sonar.projectKey=<Progect_Name>
sonar.projectName=<Progect_Name>
sonar.projectVersion=1.0 \
sonar.sources=src \
sonar.exclusions=**/protos/**,**/target/** \
sonar.language=rust \
sonar.rust.clippy.reportPaths=report.json
```

- 运行 `sonar-scanner` 即可

## 3、使用 `gitlab-ci` 自动集成sonar代码扫描

### 配置go语言项目

- 修改ci相关的shell脚本，并在ci文件中加入scanner步骤

```shell
docker run -i --rm -v $(pwd):/src -w /src <harbor-url>/golangci-lint:latest golangci-lint run -v --timeout 10m --out-format checkstyle > report.xml

docker run -i --rm -v $(pwd):/src -w /src <harbor-url>/golang:latest go test -coverprofile=coverage.out ./... ; go test -json ./... > report.json

docker run -i --rm -v $(pwd):/src -w /src <harbor-url>/sonar-scanner-cli:latest sonar-scanner -X \
  -Dsonar.host.url=http://localhost:9000 \
  -Dsonar.sourceEncoding=UTF-8 \
  -Dsonar.login=admin \
  -Dsonar.password=admin \
  -Dsonar.projectKey=<Progect_Name> \
  -Dsonar.projectName=<Progect_Name> \
  -Dsonar.projectVersion=1.0 \
  -Dsonar.go.coverage.reportPaths=coverage.out \
  -Dsonar.go.tests.reportPaths=report.json \
  -Dsonar.sources=./ \
  -Dsonar.exclusions=**/*_test.go,**/vendor/** \
  -Dsonar.language=go \
  -Dsonar.tests=. \
  -Dsonar.working.directory=/tmp \
  -Dsonar.test.inclusions=**/*_test.go \
  -Dsonar.test.exclusions=**/vendor/** \
  -Dsonar.go.golangci-lint.reportPaths=report.xml ; rm -rf report.xml coverage.out report.json
```

### 配置Java项目

- 修改ci相关的shell脚本，并在ci文件中加入scanner步骤

```shell
function scanner(){
    log "scanner begin"
    mvn ${MVN_OPTIONS} compile
    docker run -i --rm -v $(pwd):/src -w /src <harbour-url>/sonar-scanner-cli:latest sonar-scanner -X \
        -Dsonar.host.url=http://localhost:9000 \
        -Dsonar.sourceEncoding=UTF-8 \
        -Dsonar.login=admin \
        -Dsonar.password=admin \
        -Dsonar.projectKey=<Progect_Name> \
        -Dsonar.projectName=<Progect_Name> \
        -Dsonar.projectVersion=1.0 \
        -Dsonar.java.binaries=target/classes \
        -Dsonar.sources=src \
        -Dsonar.language=java \
        -Dsonar.working.directory=/tmp
}
```

### 配置JavaScript项目

- 修改gitlab-ci.yaml文件即可

```yaml
build:
  stage: build
  script:
    - scanner

.import_functions: &import_functions |

  function scanner() {
    docker run -i --rm -v $(pwd):/src -w /src <harbour-url>/sonar-scanner-cli:latest sonar-scanner -X  \
        -Dsonar.host.url=http://localhost:9000  \
        -Dsonar.sourceEncoding=UTF-8  \
        -Dsonar.login=admin  \
        -Dsonar.password=admin  \
        -Dsonar.projectKey=${CI_PROJECT_NAME}  \
        -Dsonar.projectName=${CI_PROJECT_NAME}  \
        -Dsonar.projectVersion=1.0  \
        -Dsonar.sources=src  \
        -Dsonar.language=js  \
        -Dsonar.profile=node  \
        -Dsonar.working.directory=/tmp  \
        -Dsonar.dynamicAnalysis=reuseReports  \
        -Dsonar.javascript.jstest.reportsPath=reports  \
        -Dsonar.javascript.lcov.reportPath=reports/coverage.lcov
  }

before_script:
  - *import_functions
```

### 配置Rust项目

- 修改ci相关的shell脚本，并在ci文件中加入scanner步骤
  
```shell
cargo clippy --message-format=json &> report.json

docker run -i --rm -v $(pwd):/src -w /src <harbour-url>/sonar-scanner-cli:latest sonar-scanner -X \
  -Dsonar.host.url=http://localhost:9000 \
  -Dsonar.sourceEncoding=UTF-8 \
  -Dsonar.projectKey=${CI_PROJECT_NAME} \
  -Dsonar.projectName=${CI_PROJECT_NAME} \
  -Dsonar.projectVersion=1.0 \
  -Dsonar.sources=src \
  -Dsonar.exclusions=**/protos/**,**/target/** \
  -Dsonar.language=rust \
  -Dsonar.working.directory=/tmp \
  -Dsonar.rust.clippy.reportPaths=report.json ; rm -rf report.json
```

## 4、Sonarqube从7.9升级到8.9 LTS

参见 [Sonarqbue官方升级说明](https://docs.sonarqube.org/latest/setup/upgrading/)

### 暂停旧服务

```shell
docker-compose down
```

### 删除不兼容的Plugins

不兼容的Plugin列表见：[Plugin Version Matrix](https://docs.sonarqube.org/latest/instance-administration/plugin-version-matrix/)

### 修改docker-compose.yaml文件

将

```yaml
  sonar:
    image: sonarqube:7.9-community
```

改为

```yaml
  sonar:
    image: sonarqube:8.9-community
```

即可

### 重新启动服务

```shell
docker-compose up -d
```

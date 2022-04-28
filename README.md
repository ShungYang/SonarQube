# SonarQube

使用 docker-compose 運行 SonarQube, 作為 DevSecOps 的程式碼安全性檢測的工具

## _About SonarQube_

> An automatic code review tool to detect bugs, vulnerabilities, and code smells in your code. It can integrate with your existing workflow to enable continuous code inspection across your project branches and pull requests.

## Overview

![sonarqube-pic-1](https://docs.sonarqube.org/9.4/images/dev-cycle.png)

在軟體開發流程中個元件所扮演的角色:

* `SonarLint` : 可作為 Plugin 整合至 IDE, 在軟體開發過程中即可看到程式碼是否被檢測出有安全性漏洞. 作為 Client 端的存在建議啟用 Connected Mode 連線至 SonarQube 同步 Quality Profiles, 達到開發人員程式碼規則作集中管理以及品質一致性.
* `Sonar Scanner` : 用於和 Continuous integration (CI) 工具作整合, 也將程式碼分析的結果傳至 SonarQube.
* `SonarQube` : 擁有使用者介面來呈現分析結果, 也可以進行 Quality Profiles 維護.

## Prerequisite

* Docker is required
* docker-compose is required
* 建議先在 `/etc/sysctl.conf` 中新增屬性如下

```
vm.max_map_count = 262144
```

否則運行 SonarQube Web 時會報錯 `max virtual memory areas vm.max_map_count [65530] is too low, increase to at least [262144]`

## Running SonarQube With Docker Compose

我使用 docker-compose 來運行 SonarQube 及 PostgreSQL, 內容參考 [docker-compose.yaml](https://github.com/ShungYang/SonarQube/blob/master/docker-compose.yaml)

```
sudo docker-compose up -d
```

接著訪問 <http://localhost:9000> , 使用 admin / admin 作登入後再修改密碼.

## Upgrade Guide

遇到要將 SonarQube 進行升級的話, 可以在 docker-compose.yaml 的 sonarqube.image 指定升級的 tag, `docker-compose up -d` 後前往 SonarQube webpage 會看到需要進一步 migration database 的提示, 然後使用 admin 權限登入以下網頁跟著升級指示操作.

* [http://yourSonarQubeServerURL/setup](http://yourSonarQubeServerURL/setup)

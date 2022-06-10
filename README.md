# SonarQube

> SonarQube is an open-source platform developed by SonarSource for continuous inspection of code quality. Sonar does static code analysis, which provides a detailed report of bugs, code smells, vulnerabilities, code duplications.

## _About SonarQube_

> An automatic code review tool to detect bugs, vulnerabilities, and code smells in your code. It can integrate with your existing workflow to enable continuous code inspection across your project branches and pull requests.

## Why use static code analysis tools

* 避免有安全疑慮的程式上線
* 使用掃描工具代替人工 Code review
* 建立部門使用的一致性規範
* 更乾淨, 品質更高的程式碼

## Benefits of SonarQube

* 支援掃描 17+ 程式語言
* 整合主流的 CI/CD 工具 (even Cloud Platform)
* 針對 Source Code 進行多維度的分析
* 檢查規則可自行擴充
* 與 SCM 整合後可直接看到有問題的代碼是誰寫的
* Plugin Market 優化更多分析和功能

## Overview

![sonarqube-pic-1](https://docs.sonarqube.org/9.4/images/dev-cycle.png)

在軟體開發流程中個元件所扮演的角色:

* `SonarLint` : 可作為 Plugin 整合至 IDE, 在軟體開發過程中即可看到程式碼是否被檢測出有安全性漏洞. 作為 Client 端的存在建議啟用 Connected Mode 連線至 SonarQube 同步 Quality Profiles, 達到開發人員程式碼規則作集中管理以及品質一致性.

* `SonarScanner` : 可用於和 Continuous integration (CI) 工具作整合, 在編譯程式碼專案的過程將分析的結果傳至 SonarQube.

* `SonarQube`
  * 擁有使用者介面來呈現分析結果.
  * 進行 Quality Profiles 及 Quality Gate 的維護.
  * 角色權限的管理.
  * Issue 指派.

## Prerequisite

* Docker is required
* docker-compose is required
* SonarQube developer edition 只能分析一個專案, 但是 Quality Profile 能支持 PLSQL, POC 階段我是使用 community edotion
* 建議先在 `/etc/sysctl.conf` 中新增屬性如下

```
vm.max_map_count = 262144
```

否則運行 SonarQube Web 時會報錯 `max virtual memory areas vm.max_map_count [65530] is too low, increase to at least [262144]`

## Running SonarQube With Docker Compose

* 我使用 docker-compose 來運行 SonarQube 及 PostgreSQL, 並且使用 nginx (with ssl) 作 reverse proxy, 這樣 SonarQube server 仍可維持 port 9000, 內容可參考 [docker-compose.yaml](https://github.com/ShungYang/SonarQube/blob/master/docker-compose.yaml) 以及 [nginx 的設定](https://github.com/ShungYang/SonarQube/blob/master/nginx.conf)

* 我們也可以透過[環境變數](https://docs.sonarqube.org/latest/setup/environment-variables/)來配置 SonarQube with Docker.

* 一切就序後啟動 SonarQube.

```
sudo docker-compose up -d
```

接著訪問 <http://localhost:9000> , 使用 admin / admin 作登入後再修改密碼.

* SonarQube Property

## Define Quality Profile

* 可以針對各程式語言去定義規則組合, 一個 SonarQube project 可以包含多組 Qulity Profile, 當 SonarLint 啟用 `Connected Mode` 便可以連結到指定的 SonarQube project, 用 project 中的 Qulity Profile 進行弱掃.

* 在 [.gitlab-ci.yml](https://github.com/ShungYang/SonarQube/blob/master/.gitlab-ci.yml) SonarScanner 是透過 `/k:"vscode-use-project"` 來指定 SonarQube project.

## Define Quality Gate

* 可以透過一系列的品質門檻來判斷程式碼的品質.

* 在 [.gitlab-ci.yml](https://github.com/ShungYang/SonarQube/blob/master/.gitlab-ci.yml) 是透過 `//d:sonar.qualitygate.wait="true"` 去 polling 取得 SonarQube project 上連結的 Quality Gate 結果, 未達到標準門檻狀態為 Fail, 並且 job 也會停止.

## [Metric Definitions](https://docs.sonarqube.org/latest/user-guide/metric-definitions/) - Rating

Quality Gate 中幾個維度指標的 Rating 是如何算分數的? 以 Security_rating 舉例:

### Security_rating

* A = 0 Vulnerabilities
* B = at least 1 Minor Vulnerability
* C = at least 1 Major Vulnerability
* D = at least 1 Critical Vulnerability
* E = at least 1 Blocker Vulnerability

## [SCM Integration](https://docs.sonarqube.org/latest/analysis/scm-integration/)

將 SonarQube 整合 Source Code Management ex. git / subversion 後可以使用如下功能:

* 自動將 Issue Assign 給寫 Code 的作者.
* Code Viewer 可看到哪行 Code 的作者是誰.

* __Project Settings > SCM > Key of the SCM provider for this project.__ = `git`
* or `sonar.scm.provider=git` in sonar-project.properties
* 如果 SonarScanner 是在 docker image 中執行, __請注意 .dockerignore 需要拿掉 .git__, 這樣一來 SonarScanner 才能在掃描的過程透過 .git folder 中的資訊判斷所有代碼的作者是誰.
* 須留意 git config 設定的 UserMail = SonarQube account Email.

## Upgrade Guide

遇到要將 SonarQube 進行升級的話, 可以在 docker-compose.yaml 的 sonarqube.image 指定升級的 tag, `docker-compose up -d` 後前往 SonarQube webpage 會看到需要進一步 migration database 的提示, 然後使用 admin 權限登入以下網頁跟著升級指示操作.

* [http://yourSonarQubeServerURL/setup](http://yourSonarQubeServerURL/setup)

## [Analysis Scope](https://docs.sonarcloud.io/advanced-setup/analysis-scope/)

是否可以進一步限縮分析那些資料夾或是那些檔案? 可以使用以下的萬用字元來定義分析範圍, 這些配置可以被設定在 properties 中或是直接在 SonarQube server 上配置.

Global : __Administration > General Settings > Analysis Scope.__

Language-specific : __Administration > General Settings > Languages > Your Language.__

* `*`  - Match zero or more characters
* `**` - Match zero or more directories
* `?`  - Match a single character

## PDF Report

是否可以將 SonarQube 上掃描的結果匯出成 PDF report?

* [PDF Reports are available as part of the Enterprise Edition and above.](https://docs.sonarqube.org/latest/project-administration/portfolio-pdf-configuration/)

* 由社群大神開發的 Plugin : [SonarQubeCommunity/sonar-pdf-report](https://github.com/SonarQubeCommunity/sonar-pdf-report) , 但是更新日期已經是很久以前了, 所以沒法支援到最新的 SonarQuber 版本.

## How to install plugin in SonarQube docker?

### Install

1. 由於我們使用 docker-compose 運行 SonarQube 的時候會提供一個 Volume 給它, 所以我們將下載的 Plugin 複製到 SonarQube Volume 的以下目錄 ___SONARQUBE_HOME/extensions/plugins___. 或是直接前往 __Administration > Marketplace__ 進行安裝.

2. Restart SonarQube

### Uninstall

1. Delete the plugin from the ___SONARQUBE_HOME/extensions/plugins___.

2. Restart SonarQube

## [Authentication](https://docs.sonarqube.org/latest/instance-administration/delegated-auth/)

除了由 Administrator 自行建立 Group / User 之外, SonarQube 還支援以下兩種身分驗證方式:

* SAML Authentication : 可透過 Web Page 來設定.
* LDAP Authentication : 需要透過 _$SONARQUBE-HOME/conf/sonar.properties_ 進行設定.

## [Adding Coding Rules](https://docs.sonarqube.org/latest/extend/adding-coding-rules/)

* Writing a SonarQube plugin in Java that uses SonarQube APIs to add new rules.
* Adding XPath rules directly through the SonarQube web interface.
* [Importing Third-Party Issues](https://docs.sonarqube.org/latest/analysis/external-issues/)
* [Importing Generic Issue Reports generated by an independently run tool.](https://docs.sonarqube.org/latest/analysis/generic-issue/)
* Upgrade SonarQube edition.

## [Monitoring](https://docs.sonarqube.org/latest/instance-administration/monitoring/) - Health Check

* HTTP GET : `api/system/health`

health 的狀態定義如下:

* `GREEN` : SonarQube is fully operational.
* `YELLOW` : SonarQube is usable, but it needs attention in order to be fully operational.
* `RED` : SonarQube is not operational.

[Response sample](https://next.sonarqube.com/sonarqube/web_api/api/system?query=api%2Fsystem%2Fhealth)

## Reference Link

* [Setting up SonarQube analysis for C# projects](https://www.wrightfully.com/setting-up-sonar-analysis-for-c-projects/) by Musing, Rants & Jumbled Thoughts of John M.Wright

__Enjoy!__

# SonarLint

> SonarLint is a Free and Open Source IDE extension that identifies and helps you fix quality and security issues as you code. Like a spell checker, SonarLint squiggles flaws and provides real-time feedback and clear remediation guidance to deliver clean code from the get-go.

可做為 Plugin 安裝在 IDE 上, 並透過預設的 [Rule set](https://rules.sonarsource.com/) 對 Source code 做最即時的修改建議. 也可以使用 `Connected Mode` 連上架設好的 SonarQube server 去同步 Server 上設定好的 `Quality Profile`.

這樣一來所有的開發者都可以使用同一套 Source Code 檢視的標準, 當然這應該被整合到 CI/CD Pipeline 中, 透過這種方式來做一道自動化的把關.

SonarLint 能檢查的程式語言也很豐富, 我使用的架構是在 IDE 安裝 SonarLint 插件, 並且使用 Connected Mode 連上 SonarQube server 去同步 Quality Profile 以下列出幾個專案有用到的程式語言:

## Visual Studio

* 透過`擴充功能和更新`安裝 SonarLint, 安裝好後開啟 `Team Explorer` 就能看到 SonarQube 的圖示, 輸入 Server 位置進行連接, 可參考[sonarlint-visualstudio](https://github.com/SonarSource/sonarlint-visualstudio/wiki/Connected-Mode).
* 特別是針對 .NET 專案也就是 C# 程式語言進行掃描.

## Visual Studio Code

* 透過`延伸模組`安裝 SonarLint, 接下來的設定可[參考](https://blogs.sap.com/2019/12/25/real-time-code-scan-with-sonarlint-following-sonarqube-server-rules-in-visual-studio-codevs-code/)
* 檢視 SonarLint 的插件使用說明:
  * SonarLint client 需要開發環境有安裝 jre1.8+ 的版本
  * 支援 PLSQL : 需要 SonarQube Developer Edition
  * 支援 TS /JS / Vue.js : Client 環境需要安裝 node.js 16+ 的版本

__Enjoy!__

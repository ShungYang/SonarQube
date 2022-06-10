# SonarScanner

> The SonarScanner is the scanner to use when there is no specific scanner for your build system.

我們會使用 SonarScanner 在編譯程式碼專案的過程中去進行靜態程式碼分析, SonarScanner 是一個可單獨運行的可執行檔, 並且我們會將他整合到 CI/CD Pipeline 的工具中使用, 可單獨運行的方式可分為以下兩種 :

* Running SonarScanner from the zip file
* Running SonarScanner from the Docker image

## Configuring your project

* 在程式碼專案的根目錄中建立 `sonar-project.properties`, 配置分析專案的參數, 可參考[文檔](https://docs.sonarqube.org/latest/analysis/analysis-parameters/).

* 如果沒辦法在程式碼專案的根目錄中建立 sonar-project.properties, 那還有以下兩種方式 :
  * 透過 command line 指定 project.settings 的路徑.

    ```bash
    sonar-scanner -Dproject.settings=../myproject.properties
    ```

  * 相關配置設定於 SonarQube server 上的專案, 在透過 command line 指定專案名稱.

    ```bash
    sonar-scanner -Dsonar.projectKey=myproject -Dsonar.sources=src1
    ```

## [SonarScanner for .NET](https://docs.sonarqube.org/latest/analysis/scan/sonarscanner-for-msbuild/)

針對分析 .NET 的程式碼專案, SonarScanner 有提供不同的執行檔 :

### [.NET Framework 4.6+](https://github.com/SonarSource/sonar-scanner-msbuild/releases/download/5.6.0.48455/sonar-scanner-msbuild-5.6.0.48455-net46.zip)

* 將 `$install_directory/SonarQube.Analysis.xml` 設定必要參數 : `sonar.host.url` 及 `sonar.login` (可使用 token).

* 將 `$install_directory/` 添加到 `PATH` 環境變數中.

```bash
SonarScanner.MSBuild.exe begin /k:"project-key" /d:sonar.login="<token>"
MSBuild.exe <path to solution.sln> /t:Rebuild
SonarScanner.MSBuild.exe end /d:sonar.login="<token>"
```

### Troubleshooting

如果分析失敗的話, 確認下的步驟:

* 專案是否沒有編譯成功, 而且 build 的指令必須在 begin 及 end 步驟之間.
* MSBuild 的版本是否支援? 必須 MSBuild 14.0.25420.1 以上的版本.
* __begin, build and end 步驟沒有在同一個資料夾中執行__.

### [.NET 5+](https://github.com/SonarSource/sonar-scanner-msbuild/releases/download/5.6.0.48455/sonar-scanner-msbuild-5.6.0.48455-net5.0.zip), 或是直接使用 `dotnet tool` 命令安裝

```bash
dotnet tool install --global dotnet-sonarscanner
dotnet sonarscanner begin /k:"project-key" /d:sonar.login="<token>"
dotnet build <path to solution.sln>
dotnet sonarscanner end /d:sonar.login="<token>"
```

## Provide Scan Version To SonarQube

* 在進行了靜態程式碼分析後, 我們可以在 SonarQube -> Activity 檢視分析的歷史紀錄, 如果有看到 `not provided` 代表你還沒有給予這次分析一個 Version, 除了直接從畫面上去編輯一個 Version, 你也可以在 SonarScanner 分析的過程可以透過以下參數指定一個 Scanning Version 給 SonarQube.

```bash
dotnet sonarscanner begin /k:"project-key" /v:"scanning-version"
```

__Enjoy!__

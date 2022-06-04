# SonarScanner

> The SonarScanner is the scanner to use when there is no specific scanner for your build system.

我們會使用 SonarScanner 在編譯程式碼專案的過程中去進行靜態程式碼分析, SonarScanner 是一個可單獨運行的可執行檔, 依照使用的方式可分為以下兩種 :

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

* [.NET Framework 4.6+](https://github.com/SonarSource/sonar-scanner-msbuild/releases/download/5.6.0.48455/sonar-scanner-msbuild-5.6.0.48455-net46.zip)

```bash
SonarScanner.MSBuild.exe begin /k:"project-key" /d:sonar.login="<token>"
MSBuild.exe <path to solution.sln> /t:Rebuild
SonarScanner.MSBuild.exe end /d:sonar.login="<token>"
```

* [.NET 5+](https://github.com/SonarSource/sonar-scanner-msbuild/releases/download/5.6.0.48455/sonar-scanner-msbuild-5.6.0.48455-net5.0.zip), 或是直接使用 `dotnet tool` 命令安裝.

```bash
dotnet tool install --global dotnet-sonarscanner
dotnet sonarscanner begin /k:"project-key" /d:sonar.login="<token>"
dotnet build <path to solution.sln>
dotnet sonarscanner end /d:sonar.login="<token>"
```

__Enjoy!__

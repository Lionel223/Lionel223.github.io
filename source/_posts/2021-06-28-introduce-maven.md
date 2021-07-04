---
title: Maven基本介紹
date: 2021-06-28 23:53:00
tags: ["java", "maven"]
categories: java
---

Maven 是 Apache 底下的一個專案管理工具，本文將簡單介紹 Maven 的功能，以及如何使用 Maven。

<!-- more -->

## Maven 介紹

Maven 是一個專案管理工具，它包含了專案物件模型（POM，project object model），一個標準集合，一個專案生命週期（Project Lifecycle），一個依賴管理系統（Dependency Management System）。

Maven 的主要目標就是讓開發者在最短的時間了解整個開發工作的完整狀態，為了達到這個目標，所以 Maven 專注在以下幾點：

* Making the build process easy

  簡化構建的流程，它協助開發人員屏蔽了許多細節

* Providing a uniform build system

  提供一個統一的構建系統，使用 POM（專案物件模型）以及一組 plugin 來創建專案，當熟悉了 Maven 的專案後，處理專案之間的依賴關係將花費較少的時間

* Providing quality project information

  提供良好的專案訊息，Maven 可提供許多專案訊息，這些訊息可從 POM，或者專案的 source，例如專案的依賴關係、專案的引用來源 等等

* Encouraging better development practices

  鼓勵更好的開發者實踐方式

## Maven lifecycle

Maven 將專案的構建劃分為不同的生命週期（lifecycle），可以明確的定義構建、分發至某個專案的過程，對於要構建專案的人，只要熟悉一小部分的指令就可以構建 Maven 的專案了，而 POM 則可確保他們得到所需要的結果。

當我們執行指令時，就已經使用了 lifecycle、phase、goal，例如，當執行 `mvn package` 時，實際上就執行了底下六個步驟，左邊是他的 phase，右邊是 goal

| phase                    | goal                      |
| ------------------------ | ------------------------- |
| `process-resources`      | `resources:resources`     |
| `compile`                | `compiler:compile`        |
| `process-test-resources` | `resources:testResources` |
| `test-compile`           | `compiler:testCompile`    |
| `test`                   | `surefire:test`           |
| `package`                | `jar:jar`                 |

目前有三個內建的 lifecycle，分別是 **default**、**clean**、**site**，**default** 的生命週期可以處理專案的部署，**clean** 的生命週期可以處理專案的清理，**site** 的生命週期則可處理專案項目的文檔。

生命週期都是由 phase 所組成的，例如底下是 default lifecycle 的 phase

* `validate` - 驗證專案是否正確，以及所有必要的訊息是否皆可用
* `compile` - 編譯專案的 source code
* `test` - 使用合適的單元測試來測試編譯過的 source code
* `package` - 將編譯過的 source code 打包成可發布的格式，例如 jar 檔
* `verify` - 檢查整合測試的結果，確保程式符合需求
* `install` - 將 package 安裝到 local repository，確保可以使其他專案使用
* `deploy` - 構建完成，將 package 發佈至 remote repository 提供給其他開發人員使用

## POM介紹

專案物件模型POM（Project Object Model）是 Maven 的基本工作單位，他是一個 XML 文件，記載了專案的設定與資訊，其中也包含 Maven 構建專案的資訊，以及許多專案的默認值，例如我們預設的 build 的目錄會放在目錄 `target` 內，開發主目錄在 `src/main/java`，而測試主目錄則是在 `/src/test/java`。執行 Maven 時，Maven 會先在當前目錄中找尋 POM，並讀取 POM 來取得相關設定並執行。

另外還有一種 POM 叫做 Super POM，這種 POM 是 Maven 的預設 POM，所有專案的 POM 都是從 Super POM 繼承的，也就是說 ，所有的 POM 資訊都是來自 Super POM，但 POM 的資訊也會覆蓋 Super POM 的重複項。

### 最簡單的 POM

最簡單的 POM 必須包含以下的資訊

* `project` - root
* `modelVersion` - 要設定為 `4.0.0`
* `groupId` - 專案的 group id
* `artifactId` - artifact id，可以想成是專案的名稱
* `version` - 在指定的 group 底下，這個 artifact 的版本

範例

```xml
<project>
  <modelVersion>4.0.0</modelVersion>
  <groupId>com.mycompany.app</groupId>
  <artifactId>my-app</artifactId>
  <version>1</version>
</project>
```

一個 POM 需要有 groupId、artifactId、version，這三個值可以組成專案的 fully qualified artifact name，他的格式是 `<groupId>:<artifactId>:<version>`，例如以上的範例，其 fully qualified artifact name 為 `com.mycompany.app:my-app:1`

就像前面提到的，若有些設定沒有提供，那麼就會使用預設值，例如 `packaging type` 在這邊沒有提供，就會使用預設的 `jar`。

### Project Inheritance

前面提到的 Super POM 也是其中一種專案的繼承，但也可以在 POM 內加入 `<parent>` 來放入 parent POM。

#### Example 1

##### Scenario

我們使用前面的範例，我們先前已經引入的 artifact  `com.mycompany.app:my-app:1`，現在有另外一個 artifact 名稱是 `com.mycompany.app:my-module:1`，其文件內容如下

```xml
<project>
  <modelVersion>4.0.0</modelVersion>
 
  <groupId>com.mycompany.app</groupId>
  <artifactId>my-module</artifactId>
  <version>1</version>
</project>
```

整體文件架構如下

```
.
 |-- my-module
 |   `-- pom.xml
 `-- pom.xml
```

※ *my-module/pom.xml* 是 `com.mycompany.app:my-module:1` 的 POM，*pom.xml* 則是 `com.mycompany.app:my-app:1` 的 POM

現在我們需要讓 `com.mycompany.app:my-app:1` 成為 `com.mycompany.app:my-module:1` 的 parent POM

##### Solution

此時我們需要將 `com.mycompany.app:my-app:1` 轉換成 `com.mycompany.app:my-module:1` 的 parent artifact，所以 `com.mycompany.app:my-module:1` 需要修改如下

```xml
<project>
  <modelVersion>4.0.0</modelVersion>
 
  <parent>
    <groupId>com.mycompany.app</groupId>
    <artifactId>my-app</artifactId>
    <version>1</version>
  </parent>
 
  <groupId>com.mycompany.app</groupId>
  <artifactId>my-module</artifactId>
  <version>1</version>
</project>
```

此時新增了 `<praent>`，這個欄位是用來描述這個 POM 的 parent artifact，所以必須提供完整的 artifact 名稱。

若 groupId 或 version 與 parent 的 POM 一樣，也可以不用填寫，如下範例

```xml
<project>
  <modelVersion>4.0.0</modelVersion>
 
  <parent>
    <groupId>com.mycompany.app</groupId>
    <artifactId>my-app</artifactId>
    <version>1</version>
  </parent>
 
  <version>1</version>
</project>
```

#### Example 2

##### Scenario

前一個例子使用是需要 parent POM 已經安裝到 local repository了，或者是放在特殊的檔案結構（專案 POM 的 parent POM 必須在前一層），例如檔案結構如下的話

```
.
 |-- my-module
 |   `-- pom.xml
 `-- parent
     `-- pom.xml
```

##### Solution

為了知道 parent POM 的位置，需要再加上 `<relativePath>` 欄位，所以 `com.mycompany.app:my-module:1` 要修改如下

```xml
<project>
  <modelVersion>4.0.0</modelVersion>
 
  <parent>
    <groupId>com.mycompany.app</groupId>
    <artifactId>my-app</artifactId>
    <version>1</version>
    <relativePath>../parent/pom.xml</relativePath>
  </parent>
 
  <artifactId>my-module</artifactId>
</project>
```

### Project Aggregation

Project Aggregation 就類似 Project Inheritance，但與 Project Inheritance 不同，Project Inheritance 需在 my-module 內新增 parent 欄位來設定 parent POM，而 Project Aggregation 則是在 parent POM 內設定 my-module。

透過這種方式，parent POM 就可以知道他的 module，若是在 parent POM 執行 Maven 指令的話，也可以一起執行他的 module，若要使用 Project Aggregation，則需要做以下的事情：

1. 將 parent POM 的 `<packaging>` 修改為 `pom`
2. 在 parent POM 內設定 children POM 的路徑

#### Example 1

我們再次使用前面的設定

##### Scenerio

**com.mycompany.app:my-app:1's POM**

```xml
<project>
  <modelVersion>4.0.0</modelVersion>
 
  <groupId>com.mycompany.app</groupId>
  <artifactId>my-app</artifactId>
  <version>1</version>
</project>
```

**com.mycompany.app:my-module:1's POM**

```xml
<project>
  <modelVersion>4.0.0</modelVersion>
 
  <groupId>com.mycompany.app</groupId>
  <artifactId>my-module</artifactId>
  <version>1</version>
</project>
```

**directory structure**

```
.
 |-- my-module
 |   `-- pom.xml
 `-- pom.xml
```

##### Solution

我們要將 my-module 聚合到 my-app，此時需要修改 `com.mycompany.app:my-app:1` 的 POM

```xml
<project>
  <modelVersion>4.0.0</modelVersion>
 
  <groupId>com.mycompany.app</groupId>
  <artifactId>my-app</artifactId>
  <version>1</version>
  <packaging>pom</packaging>
 
  <modules>
    <module>my-module</module>
  </modules>
</project>
```

在此處我們新增了欄位 `<packaging>` 以及 `<modules>`，`<packaging>` 的值被設定為 `pom`，`<modules>` 則被設定為 `my-module`，需注意 `<module>` 設定的是相對路徑，而不是 module 的名稱，此時在 my-app 內執行 Maven 指令時，也會在 my-module 內執行

#### Example 2

##### Scenerio

使用前面範例，但檔案結構修改如下

```
.
 |-- my-module
 |   `-- pom.xml
 `-- parent
     `-- pom.xml
```

##### Solution

與前面一樣，只要修改 `<module>` 的相對路徑即可

```xml
<project>
  <modelVersion>4.0.0</modelVersion>
 
  <groupId>com.mycompany.app</groupId>
  <artifactId>my-app</artifactId>
  <version>1</version>
  <packaging>pom</packaging>
 
  <modules>
    <module>../my-module</module>
  </modules>
</project>
```

### Project Inheritance and Project Aggregation

若有許多的 Maven 專案，且他們都有許多相同的設定時，可以將相同的部份抽出來，並做成一個 parent project，只要讓其他的 POM 都繼承這個 parent POM 即可

若有另一組專案，他們有許多的 project 需要一起 build 和 process，也可以創建一個 parent project，並將其餘的 POM 設定成 parent project 的 modules，這樣的話只要執行 parent POM，則其餘的 module 也會跟著執行

但我們也可以同時使用 Project Inheritance 和 Project Aggregation，只要做好以下三點：

1. 在每個 child project 的 POM 設定 `<parent>`
2. parent POM 內設定 `<packaging>` 改為 `pom`
3. parent POM 設定 modules，以及 child project 的相對位置

### Project Interpolation and Variables

有時需要在不同的地方使用到相同的值，這時我們就可以在一個地方定義值，在提供給其他 POM 使用，例如我們要定義 `project.version`，可以定義

```xml
<project>
  <properties>
    <version>1.0.0</version>
  </properties>
</project>
```

此時即可在其他地方如此使用

```xml
<version>${project.version}</version>
```

這個機制是在 inheritance 之後進行的，所以若 parent POM 包含了變數，這個變數也可以被 child POM 繼承並使用。

這邊共有三種可使用的變數，說明如下

#### Project Model Variables

所有 POM 內的參數都可以視為變數，例如 `${project.groupId}`、`${project.version}`、`${project.build.sourceDIrectory}`，這些字首都須加上 `project`，另外也有 `pom` 為字首的，但現在已經 deprecate 了

#### Special Variables

| name                    | description                                                  |
| ----------------------- | ------------------------------------------------------------ |
| `project.basedir`       | 此專案的位置                                                 |
| `project.baseUri`       | 此專案的位置使用 Uri表示<br />*Since Maven 2.1.0*            |
| `maven.build.timestamp` | The timestamp that denotes the start of the build (UTC). <br />*Since Maven 2.1.0-M1* |

對於build timestamp的格式，可以通過 `<maven.build.timestamp.format>`指定如下：

```xml
<project>
  ...
  <properties>
    <maven.build.timestamp.format>yyyy-MM-dd'T'HH:mm:ss'Z'</maven.build.timestamp.format>
  </properties>
  ...
</project>
```

詳情可參考 `SimpleDateFormat` 的文件，若沒有設定則會使用預設值

#### Properties

如前所述，可定義 `<properties>`，範例如下

```xml
<project>
  ...
  <properties>
    <mavenVersion>3.0</mavenVersion>
  </properties>
 
  <dependencies>
    <dependency>
      <groupId>org.apache.maven</groupId>
      <artifactId>maven-artifact</artifactId>
      <version>${mavenVersion}</version>
    </dependency>
    <dependency>
      <groupId>org.apache.maven</groupId>
      <artifactId>maven-core</artifactId>
      <version>${mavenVersion}</version>
    </dependency>
  </dependencies>
  ...
</project>
```

## Maven的項目結構

一個 Maven project，其目錄結構如下

```
maven-project
├── pom.xml
├── src
│   ├── main
│   │   ├── java
│   │   └── resources
│   └── test
│       ├── java
│       └── resources
└── target
```

maven-project 是檔案名稱，底下有一份文件是 `pom.xml`，JAVA source code會存放在 `src/main/java` 內，資源則會放在 `src/main/resources`，測試程式會放在 `src/test/java`，資源放在 `src/test/resources`，最後 compile 和 package 的文件則會放在 target 內，這就是一個 Maven project 的標準目錄結構。

## Maven的指令介紹

這個是 maven 的指令

```
mvn [options] [goal(s)] [phase(s)]
```

可以使用 `mvn -h` 來查詢如何使用

前面也有提過可以使用 mvn 指令搭配 phase 名稱，這些 phase 就會按照順序執行，例如執行了

```
mvn package
```

此時也會先進行 validate、compile、test，最後在進行 package，也可以執行多個 phase，例如

```
mvn clean package
```

此時就會先執行 clean（以及先前必要的 phase），然後再執行 package（以及先前必要的 phase）

另外還有一種是 Goal，他是更細的工作，例如

```
mvn archetype:generate
或者是
mvn checkstyle:check
```

底下列出一些常用的 phase 以及指令：

* 編譯專案

  ```
  mvn compile
  ```

* 清除專案

  ```
  mvn clean
  ```

* 將相依程式庫（JAR）複製至target/dependency目錄

  ```
  mvn dependency:copy-dependencies
  ```

* 包裝專案為JAR，放在target目錄

  ```
  mvn package
  ```

* 指定主類別執行程式

  ```
  mvn exec:java -Dexec.mainClass=packageName.className
  ```

* 刪除已下載的plugin並重新下載plugin

  ```
  mvn dependency:purge-local-repository
  ```

* 將專案產生的JAR檔，安裝至 local repository

  ```
  mvn install
  ```

## Reference

* [Maven整體認識——詳細介紹](https://codertw.com/%E7%A8%8B%E5%BC%8F%E8%AA%9E%E8%A8%80/96050/#outline__4_1_2_2_1)
* [Apache Maven Project](https://maven.apache.org/what-is-maven.html)
* [Maven Commands](http://tutorials.jenkov.com/maven/maven-commands.html)
* [Maven教學](https://kentyeh.github.io/mavenStartup/index.html)
* [Maven介紹](https://www.liaoxuefeng.com/wiki/1252599548343744/1309301146648610)
* [建構生命週期](https://openhome.cc/Gossip/JUnit/BuildLifeCycle.html)


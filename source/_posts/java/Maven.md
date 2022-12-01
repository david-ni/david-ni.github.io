---
title: Maven之Pom配置
description: Maven 是一个项目管理和整合工具。Maven 为开发者提供了一套完整的构建生命周期框架
categories: 
- Java
---

> [POM Reference](https://maven.apache.org/pom.html)

Maven 是一个项目管理和整合工具。Maven 为开发者提供了一套完整的构建生命周期框架，可以帮助开发者完成包括：打包、文档生成、依赖管理、发布等。

```xml
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">
  <modelVersion>4.0.0</modelVersion>
 
  <!-- The Basics -->
  <groupId>...</groupId>
  <artifactId>...</artifactId>
  <version>...</version>
  <packaging>...</packaging>
  <dependencies>...</dependencies>
  <parent>...</parent>
  <dependencyManagement>...</dependencyManagement>
  <modules>...</modules>
  <properties>...</properties>
 
  <!-- Build Settings -->
  <build>...</build>
  <reporting>...</reporting>
 
  <!-- More Project Information -->
  <name>...</name>
  <description>...</description>
  <url>...</url>
  <inceptionYear>...</inceptionYear>
  <licenses>...</licenses>
  <organization>...</organization>
  <developers>...</developers>
  <contributors>...</contributors>
 
  <!-- Environment Settings -->
  <issueManagement>...</issueManagement>
  <ciManagement>...</ciManagement>
  <mailingLists>...</mailingLists>
  <scm>...</scm>
  <prerequisites>...</prerequisites>
  <repositories>...</repositories>
  <pluginRepositories>...</pluginRepositories>
  <distributionManagement>...</distributionManagement>
  <profiles>...</profiles>
</project>
```

## 基本配置（The Basics）

### 继承

maven 支持继承功能。子 POM 可以使用 `parent` 指定父 POM ，然后继承其配置。

```xml
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">
  <modelVersion>4.0.0</modelVersion>
 
  <parent>
    <groupId>org.codehaus.mojo</groupId>
    <artifactId>my-parent</artifactId>
    <version>2.0</version>
    <!-- relativePath 非必需 -->
    <relativePath>../my-parent</relativePath>
  </parent>
 
  <artifactId>my-project</artifactId>
</project>
```

### 坐标

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>
  	
  	<groupId>org.me.joy</groupId>
  	<artifactId>my-project</artifactId>
  	<version>1.0</version>
</project>
```

在 maven 中，根据 `groupId`、`artifactId`、`version` 组合成 `groupId:artifactId:version` 来唯一识别一个 jar 包

- `groupId`: 定义当前 Maven 项目隶属组织或者项目的名称，一般采用域名反写，比如：`com.me.joy`
- `artifactId`: 定义当前模块的名称
- `version`: 定义当前模块的版本号
- `packaging`: 定义该项目的打包后的输出，默认为`jar`，支持的值有: `pom`,`jar`, `maven-plugin`, `ejb`, `war`, `ear`, `rar`

### 多模块

````xml
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">
  <modelVersion>4.0.0</modelVersion>
 
  <groupId>org.codehaus.mojo</groupId>
  <artifactId>my-parent</artifactId>
  <version>2.0</version>
  <packaging>pom</packaging>
 
  <modules>
    <module>my-project</module>
    <module>another-project</module>
    <module>third-project/pom-example.xml</module>
  </modules>
</project>
````

### 自定义属性

```xml
<properties>
  	<!--- ${spring.cloud.version} -->
		<spring.boot.version>1.5.15.RELEASE</spring.boot.version>
</properties>
```

### 依赖

```xml
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">
  ...
  <dependencies>
    <dependency>
      <groupId>junit</groupId>
      <artifactId>junit</artifactId>
      <version>4.12</version>
      <type>jar</type>
      <scope>test</scope>
      <optional>true</optional>
    </dependency>
    ...
  </dependencies>
  ...
</project>
```

#### `type`

依赖的类型，默认为`jar``

#### `optional`

当模块本身作为依赖时，设定这个依赖是可选的 

#### `scope`

控制依赖的使用范围。通俗的讲，就是控制依赖在哪些范围被加载和使用。

- `compile`: 默认值，被依赖项目需要参与到当前项目的编译，测试，打包，运行等阶段。打包的时候通常会包含被依赖项目。

- `provided`：被依赖项目理论上可以参与编译、测试、运行等阶段，相当于`compile`，但是在打包阶段做了`exclude`的动作。例如， 如果我们在开发一个web 应用，在编译时我们需要依赖 servlet-api.jar，但是在运行时我们不需要该 jar 包，因为这个 jar 包已由应用服务器提供，此时我们需要使用 provided 进行范围修饰。

- `runtime`: 表示被依赖项目无需参与项目的编译，但是会参与到项目的测试和运行。与compile相比，被依赖项目无需参与项目的编译。例如，在编译的时候我们不需要 JDBC API 的 jar 包，而在运行的时候我们才需要 JDBC 驱动包。

- `test`: 表示被依赖项目仅仅参与测试相关的工作，包括测试代码的编译，执行。例如，Junit 测试。

- `system`: `system`与`provided` 元素类似，但是被依赖项不会从 maven 仓库中查找，而是从本地系统中获取，systemPath 元素用于制定本地系统中 jar 文件的路径。例如:

  ```xml
  <dependency>
      <groupId>org.open</groupId>
      <artifactId>open-core</artifactId>
      <version>1.5</version>
      <scope>system</scope>
      <systemPath>${basedir}/WebContent/WEB-INF/lib/open-core.jar</systemPath>
  </dependency>
  ```

- `import`: **只能用在<dependencyManagement>里面**

  ```xml
  <dependencyManagement>
      <dependencies>
          <dependency>
              <groupId>org.springframework.boot</groupId>
              <artifactId>spring-boot-dependencies</artifactId>
              <version>2.1.6.RELEASE</version>
              <type>pom</type>
              <scope>import</scope>
          </dependency>
      </dependencies>
  </dependencyManagement>
  ```

  它的意思是将`spring-boot-dependencies` 中`dependencyManagement`的`dependencies`，全部引入到当前工程的`dependencyManagement`中。

  > **scope的依赖传递**
  >
  > A–>B–>C。当前项目为A，A依赖于B，B依赖于C。知道B在A项目中的scope，那么怎么知道C在A中的scope呢？答案是：
  > 当C是test或者provided时，C直接被丢弃，A不依赖C；否则A依赖C，C的scope继承于B的scope。

## 构建配置（Build Settings）

在maven中构建配置可以分为 "project build" 和 "profile build"。

```xml
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">
  ...
  <!-- "Project Build" contains more elements than just the BaseBuild set -->
  <build>...</build>
 
  <profiles>
    <profile>
      <!-- "Profile Build" contains a subset of "Project Build"s elements -->
      <build>...</build>
    </profile>
  </profiles>
</project>
```

### 基本配置

基本配置在`project build` 和 `profile build`中都是适用的

```xml
<build>
  <defaultGoal>install</defaultGoal>
  <directory>${basedir}/target</directory>
  <finalName>${artifactId}-${version}</finalName>
  <filters>
    <filter>filters/filter1.properties</filter>
  </filters>
  ...
</build>
```

- `defaultGoal`: 执行构建时默认的goal或phase

- `directory`：构建结果所在的路径，默认为${basedir}/target目录

- `finalName`：构建的最终结果的名字，该名字可能在其他plugin中被改变，默认是`${artifactId}-${version}`

- `filter`:  定义 `*.properties` 文件，过滤器文件中定义的“name = value”对在代码中替换$ {name}字符串

  举个例子我们通常将配置定义在`application.properties`中

  ```yaml
  # application.properties
  application.name=${project.name}
  message=${custom.message}
  ```

  而`${project.name}`/`${custom.message}`这些属性的具体值通常被定义在`pom.xml`、`settings.xml`、系统变量或者`filter`指定的文件中

#### Resource

```xml
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">
  <build>
    ...
    <resources>
      <resource>
        <targetPath>META-INF/plexus</targetPath>
        <filtering>false</filtering>
        <directory>${basedir}/src/main/plexus</directory>
        <includes>
          <include>configuration.xml</include>
        </includes>
        <excludes>
          <exclude>**/*.properties</exclude>
        </excludes>
      </resource>
    </resources>
    <testResources>
      ...
    </testResources>
    ...
  </build>
</project>
```

- **targetPath**: 指定从构建中放置资源集的目录结构。目标路径默认为基本目录。将要包装在 jar 中的资源的通常指定的目标路径是 META-INF。

- **filtering**: 值为 true 或 false。表示是否要为此资源启用过滤。及是否替换`${property name}`

- **directory**: 值定义了资源的路径。构建的默认目录是`${basedir}/src/main/resources`。

- **includes**: 一组文件匹配模式，指定目录中要包括的文件，使用*作为通配符。

- **excludes**: 与 `includes` 类似，指定目录中要排除的文件，使用*作为通配符。注意：如果 `include` 和 `exclude` 发生冲突，maven 会以 `exclude` 作为有效项。

- **testResources**: `testResources` 与 `resources` 功能类似，区别仅在于：`testResources` 指定的资源仅用于 test 阶段，并且其默认资源目录为：`${basedir}/src/test/resources` 。

#### Plugin

```xml
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">
  <build>
    ...
    <plugins>
      <plugin>
        <groupId>org.apache.maven.plugins</groupId>
        <artifactId>maven-jar-plugin</artifactId>
        <version>2.6</version>
        <extensions>false</extensions>
        <inherited>true</inherited>
        <configuration>
          <classifier>test</classifier>
        </configuration>
        <dependencies>...</dependencies>
        <executions>...</executions>
      </plugin>
    </plugins>
  </build>
</project>
```

#### PluginManagement

与 `dependencyManagement` 很相似，在当前 POM 中仅声明插件，而不是实际引入插件。子 POM 中只配置 `groupId` 和 `artifactId` 就可以完成插件的引用，且子 POM 有权重写 pluginManagement 定义。

它的目的在于统一所有子 POM 的插件版本。



### 仅适用于`project build`

#### Directories

```xml
<project xmlns="http://maven.apache.org/POM/4.0.0"
  xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xsi:schemaLocation="http://maven.apache.org/POM/4.0.0
                      https://maven.apache.org/xsd/maven-4.0.0.xsd">
  ...
  <build>
    <sourceDirectory>${basedir}/src/main/java</sourceDirectory>
    <scriptSourceDirectory>${basedir}/src/main/scripts</scriptSourceDirectory>
    <testSourceDirectory>${basedir}/src/test/java</testSourceDirectory>
    <outputDirectory>${basedir}/target/classes</outputDirectory>
    <testOutputDirectory>${basedir}/target/test-classes</testOutputDirectory>
    ...
  </build>
</project>
```

目录元素集合存在于 `build` 元素中，它为整个 POM 设置了各种目录结构。由于它们在配置文件构建中不存在，所以这些不能由配置文件更改。

如果上述目录元素的值设置为绝对路径（扩展属性时），则使用该目录。否则，它是相对于基础构建目录：`${basedir}`。



#### Extensions

```xml
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">
  ...
  <build>
    ...
    <extensions>
      <extension>
        <groupId>org.apache.maven.wagon</groupId>
        <artifactId>wagon-ftp</artifactId>
        <version>1.0-alpha-3</version>
      </extension>
    </extensions>
    ...
  </build>
</project>
```

## 环境配置（Environment Settings）

### IssueManagement

这定义了所使用的缺陷跟踪系统（Bugzilla，TestTrack，ClearQuest 等）。它主要用于生成项目文档。

```xml
<project xmlns="http://maven.apache.org/POM/4.0.0"
  xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xsi:schemaLocation="http://maven.apache.org/POM/4.0.0
                      https://maven.apache.org/xsd/maven-4.0.0.xsd">
  ...
  <issueManagement>
    <system>Bugzilla</system>
    <url>http://127.0.0.1/bugzilla/</url>
  </issueManagement>
  ...
</project>
```

### CiManagement

CI 构建系统配置，主要是指定通知机制以及被通知的邮箱。

```xml
<project xmlns="http://maven.apache.org/POM/4.0.0"
  xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xsi:schemaLocation="http://maven.apache.org/POM/4.0.0
                      https://maven.apache.org/xsd/maven-4.0.0.xsd">
  ...
  <ciManagement>
    <system>continuum</system>
    <url>http://127.0.0.1:8080/continuum</url>
    <notifiers>
      <notifier>
        <type>mail</type>
        <sendOnError>true</sendOnError>
        <sendOnFailure>true</sendOnFailure>
        <sendOnSuccess>false</sendOnSuccess>
        <sendOnWarning>false</sendOnWarning>
        <configuration><address>continuum@127.0.0.1</address></configuration>
      </notifier>
    </notifiers>
  </ciManagement>
  ...
</project>
```

### mailingLists

邮件列表，通常指定开发者或用户的邮箱

```xml
<project xmlns="http://maven.apache.org/POM/4.0.0"
  xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xsi:schemaLocation="http://maven.apache.org/POM/4.0.0
                      https://maven.apache.org/xsd/maven-4.0.0.xsd">
  ...
  <mailingLists>
    <mailingList>
      <name>User List</name>
      <subscribe>user-subscribe@127.0.0.1</subscribe>
      <unsubscribe>user-unsubscribe@127.0.0.1</unsubscribe>
      <post>user@127.0.0.1</post>
      <archive>http://127.0.0.1/user/</archive>
      <otherArchives>
        <otherArchive>http://base.google.com/base/1/127.0.0.1</otherArchive>
      </otherArchives>
    </mailingList>
  </mailingLists>
  ...
</project>
```

### SCM

SCM（软件配置管理，也称为源代码/控制管理或简洁的版本控制）。常见的 scm 有 svn 和 git 。

```xml
<project xmlns="http://maven.apache.org/POM/4.0.0"
  xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xsi:schemaLocation="http://maven.apache.org/POM/4.0.0
                      https://maven.apache.org/xsd/maven-4.0.0.xsd">
  ...
  <scm>
    <connection>scm:svn:http://127.0.0.1/svn/my-project</connection>
    <developerConnection>scm:svn:https://127.0.0.1/svn/my-project</developerConnection>
    <tag>HEAD</tag>
    <url>http://127.0.0.1/websvn/my-project</url>
  </scm>
  ...
</project>
```

### Prerequisites

POM 执行的预设条件。

```
<project xmlns="http://maven.apache.org/POM/4.0.0"
  xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xsi:schemaLocation="http://maven.apache.org/POM/4.0.0
                      https://maven.apache.org/xsd/maven-4.0.0.xsd">
  ...
  <prerequisites>
    <maven>2.0.6</maven>
  </prerequisites>
  ...
</project>
复制代码
```

### Repositories

Maven仓库配置，默认的 Maven 中央存储库位于https://repo.maven.apache.org/maven2/上。

```xml
<project xmlns="http://maven.apache.org/POM/4.0.0"
  xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xsi:schemaLocation="http://maven.apache.org/POM/4.0.0
                      https://maven.apache.org/xsd/maven-4.0.0.xsd">
  ...
  <repositories>
    <repository>
      <releases>
        <enabled>false</enabled>
        <updatePolicy>always</updatePolicy>
        <checksumPolicy>warn</checksumPolicy>
      </releases>
      <snapshots>
        <enabled>true</enabled>
        <updatePolicy>never</updatePolicy>
        <checksumPolicy>fail</checksumPolicy>
      </snapshots>
      <id>codehausSnapshots</id>
      <name>Codehaus Snapshots</name>
      <url>http://snapshots.maven.codehaus.org/maven2</url>
      <layout>default</layout>
    </repository>
  </repositories>
  <pluginRepositories>
    ...
  </pluginRepositories>
  ...
</project>
```

- `releases, snapshots`:  定义不同包的类型的策略
- `enabled`：是否启用策略
- `updatePolicy`：更新策略，可选项有：`always`, `daily` (默认), `interval:X` (x代表分钟) or `never`.
- `checksumPolicy`：checksum校验策略，可选项有 `ignore`, `fail`, or `warn`

### PluginRepositories

与 `repositories` 差不多。

```
<project xmlns="http://maven.apache.org/POM/4.0.0"
  xmlns:xsi="http://www.w3.org/001/XMLSchema-instance"
  xsi:schemaLocation="http://maven.apache.org/POM/4.0.0
                      https://maven.apache.org/xsd/maven-4.0.0.xsd">
  ...
  <distributionManagement>
    ...
    <downloadUrl>http://mojo.codehaus.org/my-project</downloadUrl>
    <status>deployed</status>
  </distributionManagement>
  ...
</project>
复制代码
```

### DistributionManagement

它管理在整个构建过程中生成的 artifact 和支持文件的分布。从最后的元素开始：

```xml
<project xmlns="http://maven.apache.org/POM/4.0.0"
  xmlns:xsi="http://www.w3.org/001/XMLSchema-instance"
  xsi:schemaLocation="http://maven.apache.org/POM/4.0.0
                      https://maven.apache.org/xsd/maven-4.0.0.xsd">
  ...
  <distributionManagement>
    <repository>
    		<id>releases</id>
        <name>Release</name>
        <url>http://192.168.30.5:8081/nexus/content/repositories/releases</url>
    </repository>
    <snapshotRepository>
        <id>snapshot</id>
        <name>SnapShot</name>
        <url>http://192.168.30.5:8081/nexus/content/repositories/snapshots</url>
    </snapshotRepository>
  </distributionManagement>
  ...
</project>
```

### Profiles

定义不同环境下的配置

```xml
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">
  ...
  <profiles>
    <profile>
      <id>test</id>
      <activation>...</activation>
      <build>...</build>
      <modules>...</modules>
      <repositories>...</repositories>
      <pluginRepositories>...</pluginRepositories>
      <dependencies>...</dependencies>
      <reporting>...</reporting>
      <dependencyManagement>...</dependencyManagement>
      <distributionManagement>...</distributionManagement>
    </profile>
  </profiles>
</project>
```




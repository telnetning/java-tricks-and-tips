####命令行下建立 maven 项目
maven 是 Java 项目中常用的项目管理程序。这里记录在命令行下如何用 maven 建立一个 Java 项目。  

首先安装 maven，这个在 Unix/Linux 下直接包管理器安装，比如 OS X 下：

```sh
brew install maven
```

安装完成之后，

```sh
mvn -v  #查看 maven 版本
mvn -h  #查看 maven 命令行参数
```

然后我们建立一个项目，在一个空目录下：

```sh
mvn archetype:generate -DgroupId=com.telnetning.helloworld -DartifactId=helloworld -Dpackage=com.telnetning.helloworld -Dversion=1.0
```

这里的 archetype 是 maven 下的一个插件，提供了一些基本的项目模型供选择，比如 J2EE，这里直接选 maven-archetype-quickstart，模型如其名。  

另外，maven 的参数中 -D 可以用来进行项目设置，这里设置了 groupId、artifactId、package 以及 version：  

```sh
groupId - the id of the project's group.
artifactId - the id of the artifact (project)
version - the version of the artifact under the specified group
```

其中一般起码设置 groupId、artifactId 以及 version，这三者构成了一个唯一的应用版本：groupId 代表公司或者单位项目组名，artifactId 代表项目名，version 代表项目版本号 -- <groupId>:<artifactId>:<version>  

另外默认会指定包类型为 jar。

完成之后 `tree -l 2` 能看到目录结构为：  

```sh
➜  helloworld tree -L 2
.
├── pom.xml
└── src
    ├── main
    └── test

3 directories, 1 file
```

然后 `mvn package` 编译，会生成 target 目录，该目录下为编译的结果。  

```sh
➜  helloworld tree -L 2
.
├── 2
├── pom.xml
├── src
│   ├── main
│   └── test
└── target
    ├── classes
    ├── helloworld-1.0.jar
    ├── maven-archiver
    ├── maven-status
    ├── surefire-reports
    └── test-classes

9 directories, 3 files
```

运行程序：  

```sh
java -cp target/helloworld-1.0.jar com.telnetning.helloworld.App
```

-cp 是指定文件搜索路径的意思。



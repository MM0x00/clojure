# clojure 在线实验环境

## 软件简介

Clojure是Lisp编程语言的一种方言。它是一种通用编程语言，重点是功能编程。它运行在Java虚拟机，通用语言运行时和JavaScript引擎上。像其他Lisps一样，Clojure将代码视为数据，并具有宏系统。

所属类别是编程语言

License：EPL-1.0

特点：

1.动态发展

2.LISP

3.并发编程

4.功能编程

5.运行时多态

6.托管在JVM上

## 软件官网

https://www.clojure.org/

## Dockerfile 使用方法

### 在您的应用程序中启动Lein / Clojure实例

由于使用Clojure最常见的方法是与Leiningen（lein）配合使用，所以这个图像假设你将会如何工作。使用此图像的最直接的方法是添加Dockerfile到现有的Leiningen / Clojure项目中：
```
FROM clojure
COPY . /usr/src/app
WORKDIR /usr/src/app
CMD ["lein", "run"]
```
然后，运行这些命令来构建和运行映像：
```
$ docker build -t my-clojure-app .
$ docker run -it --rm --name my-running-app my-clojure-app
```
虽然上述是一个最直接的例子Dockerfile，但它确实有一些缺点。该lein run命令将下载您的依赖项，编译项目，然后运行它。这是很多工作，所有这些都可能不需要在每次运行图像时完成。为了解决这个问题，您可以下载相关程序并提前编译项目。当您运行图像时，这将大大减少启动时间。
```
FROM clojure
RUN mkdir -p /usr/src/app
WORKDIR /usr/src/app
COPY project.clj /usr/src/app/
RUN lein deps
COPY . /usr/src/app
RUN mv "$(lein uberjar | sed -n 's/^Created \(.*standalone\.jar\)/\1/p')" app-standalone.jar
CMD ["java", "-jar", "app-standalone.jar"]
```
以Dockerfile这种方式编写将下载依赖关系（并缓存它们，因此只有在依赖关系发生变化时才会重新下载），然后将其逐次编译成独立的jar，而不是每次运行映像。

然后，您可以如上所述构建和运行映像。

### 将您的Lein / Clojure项目从容器内的一个罐子中编译出来
如果您有一个现有的Lein / Clojure项目，将您的项目从容器中编译到jar中是非常简单的：
```
$ docker run -it --rm -v "$PWD":/usr/src/app -w /usr/src/app clojure lein uberjar
```
这将把您的项目构建到位于项目目录中的jar文件中target/uberjar

## 资源链接

- https://www.clojure.org/

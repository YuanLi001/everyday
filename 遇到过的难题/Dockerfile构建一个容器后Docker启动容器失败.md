大二上在和学长一起做一个项目的时候，Docker启动一个项目失败。

思考与解决过程：

当时刚学docker，情况也比较紧急，另一个系统的同学已经用docker把系统部署上去了，我们这个系统也在催

##### 1.启动容器失败，使用docker ps，找不到失败的容器

经过搜索，得知：

docker ps命令用来展示所有运行中的容器

docker ps -a命令是用来展示所有所有的容器，包括未运行的容器

##### 2.使用docker ps -a看到了失败的容器

##### 3.使用docker logs查看启动失败的容器的日志

错误原因是找不到mainclass

##### 4.检查了好几遍Dockerfile，发现不是自己的问题

##### 5.继续搜素，发现和项目启动类有关，需要配置启动类什么的（于是我去spring官网，根据文档，配置一下pom.xml里的plugin成功解决）

```xml
<build>
    <plugins>
        <plugin>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-maven-plugin</artifactId>
            <configuration>
                <mainClass>${start.class}</mainClass>
                <layout>JAR</layout>
            </configuration>
            <executions>
                <execution>
                    <goals>
                        <goal>repackage</goal>
                    </goals>
                </execution>
            </executions>
        </plugin>
    </plugins>
</build>
```

##### 6.还没完，重点来了

非常疑惑，平常的SpringBoot项目都是打个Jar包，然后就能运行，没什么事。为什么这次就不行了？

我又去仔细看了另一个系统的pom.xml,发现他们的pom.xml里初始化和我们一样，都是SpringBoot项目初始化的样子。但经过几遍重复对比，最终发现了差异。我们的项目的父pom里没有spring-boot-starter-parent

```xml
<parent>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-parent</artifactId>
    <version>2.1.8.RELEASE</version>
    <relativePath/> <!-- lookup parent from repository -->
</parent>
```

##### 7.那么为什么没有spring-boot-starter-parent就需要自己配置

首先看看spring-boot-starter-parent的功能：

1. 定义了 Java 编译版本为 1.8 。
2. 使用 UTF-8 格式编码。
3. 继承自 `spring-boot-dependencies`，这个里边定义了依赖的版本，也正是因为继承了这个依赖，所以我们在写依赖时才不需要写版本号。
4. 执行打包操作的配置。
5. 自动化的资源过滤。
6. 自动化的插件配置。
7. 针对 application.properties 和 application.yml 的资源过滤，包括通过 profile 定义的不同环境的配置文件，例如 application-dev.properties 和 application-dev.yml。

所以：关于打包的插件、编译的 JDK 版本、文件的编码格式等等这些配置，在没有 parent 的时候，要自己去配置。

关于spring-boot-starter-parent的详细内容：https://yuanli.site/2022/01/23/spring-boot-starter-parent/
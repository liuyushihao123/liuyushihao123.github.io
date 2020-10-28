---
layout: post
title:  "当Docker遇到Intellij IDEA，再次解放了生产力~"
categories: docker
tags:  docker
author: DuGuYu
---

点击上方蓝色“方志朋”，选择“设为星标”

回复“666”获取独家整理的学习资料！![](https://mmbiz.qpic.cn/mmbiz_jpg/ow6przZuPIENb0m5iawutIf90N2Ub3dcPuP2KXHJvaR1Fv2FnicTuOy3KcHuIEJbd9lUyOibeXqW8tEhoJGL98qOw/640?wx_fmt=jpeg)

> 转自：陶章好链接：https://juejin.im/post/5d026212f265da1b8608828b

Idea是Java开发利器，SpringBoot是Java生态中最流行的微服务框架，docker是时下最火的容器技术，那么它们结合在一起会产生什么化学反应呢？  
  

## **一、开发前准备**

#### 1\. Docker的安装可以参考https://docs.docker.com/install/

####   

#### 2\. 配置docker远程连接端口

```nginx
  vi /usr/lib/systemd/system/docker.service
```

  
找到 ExecStart，在最后面添加 -H tcp://0.0.0.0:2375，如下图所示  

  

![](https://github.com/liuyushihao123/image/blob/master/%E5%A6%82%E4%B8%8B%E5%9B%BE%E6%89%80%E7%A4%BA.jpg?raw=true)

  
  

#### 3\. 重启docker

```properties
 systemctl daemon-reload
 systemctl start docker
```

  

#### 4\. 开放端口

```cs
firewall-cmd --zone=public --add-port=2375/tcp --permanent
```

  

#### 5\. Idea安装插件,重启

  

![](https://mmbiz.qpic.cn/mmbiz/R5ic1icyNBNd6DRjsOibGuiatVMevkicOLTibxotftyNlcU3yPCSu6BzdncxLccqVlS28UdvexnEic9fTaWOE31SU7J7g/640?wx_fmt=other)

  

#### 6\. 连接远程docker

  

####  \(1\) 编辑配置

![](https://mmbiz.qpic.cn/mmbiz/R5ic1icyNBNd6DRjsOibGuiatVMevkicOLTibxKV0hZxxvjUh3LBpW5uJpx7Fu4Db8PDliaDjuI9RR0GZWOcHaeTUJsUw/640?wx_fmt=other)

#### \(2\) 填远程docker地址

![](https://mmbiz.qpic.cn/mmbiz/R5ic1icyNBNd6DRjsOibGuiatVMevkicOLTibxNyervibcOibZWxPz7KICicc5UPPANxb3qNic4xWcXIMM8WMuuhJIdxI7Dw/640?wx_fmt=other)

####   

####  \(3\) 连接成功，会列出远程docker容器和镜像

![](https://mmbiz.qpic.cn/mmbiz/R5ic1icyNBNd6DRjsOibGuiatVMevkicOLTibxIbcia6KFBWUmNUicHBBWwibO3UdmicPkElwZU9DhnfXap5nJ9EQP9wmLKA/640?wx_fmt=other)

  

## **二、新建项目**

  

#### 1\. 创建springboot项目

  
项目结构图  

![](https://mmbiz.qpic.cn/mmbiz/R5ic1icyNBNd6DRjsOibGuiatVMevkicOLTibxSAIiadk5icuRVCrKich9ot9Kicicuia1WjQcRkfEYWheZdPQrZVPS4M275Hg/640?wx_fmt=other)

  

#### \(1\) 配置pom文件

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <groupId>docker-demo</groupId>
    <artifactId>com.demo</artifactId>
    <version>1.0-SNAPSHOT</version>
    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.0.2.RELEASE</version>
        <relativePath />
    </parent>

    <properties>
         <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
         <project.reporting.outputEncoding>UTF-8</project.reporting.outputEncoding>
         <docker.image.prefix>com.demo</docker.image.prefix>
         <java.version>1.8</java.version>
    </properties>
    <build>
        <plugins>
          <plugin>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-maven-plugin</artifactId>
          </plugin>
        <plugin>
           <groupId>com.spotify</groupId>
           <artifactId>docker-maven-plugin</artifactId>
           <version>1.0.0</version>
           <configuration>
              <dockerDirectory>src/main/docker</dockerDirectory>
              <resources>
                <resource>
                    <targetPath>/</targetPath>
                    <directory>${project.build.directory}</directory>
                    <include>${project.build.finalName}.jar</include>
                </resource>
              </resources>
           </configuration>
        </plugin>
        <plugin>
            <artifactId>maven-antrun-plugin</artifactId>
            <executions>
                 <execution>
                     <phase>package</phase>
                    <configuration>
                        <tasks>
                            <copy todir="src/main/docker" file="target/${project.artifactId}-${project.version}.${project.packaging}"></copy>
                        </tasks>
                     </configuration>
                    <goals>
                        <goal>run</goal>
                    </goals>
                    </execution>
            </executions>
        </plugin>

       </plugins>
    </build>
<dependencies>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-web</artifactId>
    </dependency>
    <dependency>
  <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-test</artifactId>
        <scope>test</scope>
    </dependency>
    <dependency>
        <groupId>log4j</groupId>
        <artifactId>log4j</artifactId>
        <version>1.2.17</version>
    </dependency>
</dependencies>
</project>
```

  

#### \(2\) 在src/main目录下创建docker目录，并创建Dockerfile文件

  

```nginx
FROM openjdk:8-jdk-alpine
ADD *.jar app.jar
ENTRYPOINT ["java","-Djava.security.egd=file:/dev/./urandom","-jar","/app.jar"]
```

  

#### \(3\) 在resource目录下创建application.properties文件

  

```javascript
logging.config=classpath:logback.xml
logging.path=/home/developer/app/logs/
server.port=8990
```

  

#### \(4\) 创建DockerApplication文件

  

```java
@SpringBootApplication
public class DockerApplication {
    public static void main(String[] args) {
        SpringApplication.run(DockerApplication.class, args);
    }
}
```

  

#### \(5\) 创建DockerController文件

  

```kotlin
@RestController
public class DockerController {
    static Log log = LogFactory.getLog(DockerController.class);

    @RequestMapping("/")
    public String index() {
        log.info("Hello Docker!");
        return "Hello Docker!";
    }
}
```

  

#### \(6\) 增加配置

  

![](https://mmbiz.qpic.cn/mmbiz/R5ic1icyNBNd6DRjsOibGuiatVMevkicOLTibxicolX3pqocjfFliaFJBJ1aFW15GXBYtWX9KAVCEeNYicIJcB8gcV0Ojdw/640?wx_fmt=other)

  

![](https://mmbiz.qpic.cn/mmbiz/R5ic1icyNBNd6DRjsOibGuiatVMevkicOLTibxddlIsNwZqNPiaLrxqe29bULl1jcoXibMEUL8YylClKs68Q6KEIias0abw/640?wx_fmt=other)

  

![](https://mmbiz.qpic.cn/mmbiz/R5ic1icyNBNd6DRjsOibGuiatVMevkicOLTibx2RqSRD30VECKnrliaXnCo88sSexaU03Fick1qT5KHLa1ibQ6G0F0U5pfw/640?wx_fmt=other)

  
**命令解释**  
  
Image tag : 指定镜像名称和tag，镜像名称为 docker-demo，tag为1.1  
Bind ports : 绑定宿主机端口到容器内部端口。格式为\[宿主机端口\]:\[容器内部端口\]Bind mounts : 将宿主机目录挂到到容器内部目录中。格式为\[宿主机目录\]:\[容器内部目录\]。这个springboot项目会将日志打印在容器 /home/developer/app/logs/ 目录下，将宿主机目录挂载到容器内部目录后，那么日志就会持久化容器外部的宿主机目录中。  

#### \(7\) Maven打包

  

![](https://mmbiz.qpic.cn/mmbiz/R5ic1icyNBNd6DRjsOibGuiatVMevkicOLTibxzKd8SCoz9G4fVv88kOuZjD604ardFKbz1ScEC0TaO94x5ib7o2kyXjA/640?wx_fmt=other)

  

#### \(8\) 运行

  

![](https://mmbiz.qpic.cn/mmbiz/R5ic1icyNBNd6DRjsOibGuiatVMevkicOLTibxLzd9Nu2F0NczVPydXwa8AwK9vibL3dyib4MncaCgxPabLDE52G2E5JKw/640?wx_fmt=other)

![](https://mmbiz.qpic.cn/mmbiz/R5ic1icyNBNd6DRjsOibGuiatVMevkicOLTibxskhWWMKoQsuef7Sz84Pv8hcFibILQlNHLwWwttGHBBMw0npa1ianu9wQ/640?wx_fmt=other)

  
先pull基础镜像，然后再打包镜像，并将镜像部署到远程docker运行  

![](https://mmbiz.qpic.cn/mmbiz/R5ic1icyNBNd6DRjsOibGuiatVMevkicOLTibx7xQSGbsyPKKhqBDyrv6xmMhHURibCFnq9vMdV4r4R4TnHdtn1AxUF6w/640?wx_fmt=other)

这里我们可以看到镜像名称为docker-demo:1.1，docker容器为docker-server  

#### \(9\) 运行成功

  

![](https://mmbiz.qpic.cn/mmbiz/R5ic1icyNBNd6DRjsOibGuiatVMevkicOLTibx0gUI2tlCtDCADuJoI38cNs1QNH9VFNkKgJiacDMuGx4VHgMQiaAJJSVw/640?wx_fmt=other)

  

#### \(10\) 浏览器访问

  

![](https://mmbiz.qpic.cn/mmbiz/R5ic1icyNBNd6DRjsOibGuiatVMevkicOLTibxHibicPCQn7fM5Snd5OyAfnxt8oFWCILiaL60Xvk1hWUbdibC6anPibp9L2A/640?wx_fmt=other)

\(11\) 日志查看  

![](https://mmbiz.qpic.cn/mmbiz/R5ic1icyNBNd6DRjsOibGuiatVMevkicOLTibxsYD5GMrBnRT1YTDVRmh7G86MN7XEOH5Z4sHWibuP9rCVDgFysR8judg/640?wx_fmt=other)

  
自此通过idea 部署springboot项目到docker成功！难以想象，部署一个Javaweb项目竟然如此简单方便！  

**热门内容：**

- [监控、链路追踪、日志这三者有何区别？](http://mp.weixin.qq.com/s?__biz=MzAxNjk4ODE4OQ==&mid=2247494403&idx=2&sn=e4991351bd7dbcf732d5c2052a3087b9&chksm=9beeca71ac994367069f22f71d5dd3ae4971b10404f51a3f7c38f9255b66e2c0099afdfb0301&scene=21#wechat_redirect)
- [为什么建议使用你 LocalDateTime ，而不是 Date？](http://mp.weixin.qq.com/s?__biz=MzAxNjk4ODE4OQ==&mid=2247494347&idx=1&sn=039ede3d66d770bf4abb2849c2880cf3&chksm=9beecbb9ac9942af81b40aa178a04a2065050b119b3dd097808be91f62691a76bd32bb903bcb&scene=21#wechat_redirect)  
- [突然就懵了！面试官问我：线程池中多余的线程是如何回收的？](http://mp.weixin.qq.com/s?__biz=MzAxNjk4ODE4OQ==&mid=2247494323&idx=1&sn=80f5c1f05558ff096639a67542c0cc09&chksm=9beecbc1ac9942d7ae65d9988e2e5376b04255bb537037b05d14ea5530642a5faefc20c36baf&scene=21#wechat_redirect)  
- [IntelliJ IDEA 15款 神级超级牛逼插件推荐（自用，真的超级牛逼）](http://mp.weixin.qq.com/s?__biz=MzAxNjk4ODE4OQ==&mid=2247494297&idx=1&sn=e0c6958bc56796b8db317d5f606e8f75&chksm=9beecbebac9942fd8c02faa12cb7f01df77d2af62d861223195e384afc19171ce3817388b2d4&scene=21#wechat_redirect)

  

![](https://mmbiz.qpic.cn/mmbiz_png/rtJ5LhxxzwmeeDF9fPPQbQ24cJV10eLE8S3wgTkdrg5ZMg02G0Nh0TCnYtajMyr1SuW0Wup1iaaqRYZxcKAYZHQ/640?wx_fmt=jpeg)

最近面试BAT，整理一份面试资料《**Java面试BAT通关手册**》，覆盖了Java核心技术、JVM、Java并发、SSM、微服务、数据库、数据结构等等。获取方式：点“**在看**”，关注公众号并回复 **666** 领取，更多内容陆续奉上。

**明天见\(｡･ω･｡\)ﾉ♡**

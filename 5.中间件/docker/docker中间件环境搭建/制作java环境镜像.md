<!--
 * @Descripttion: 
 * @Author: guox
 * @Date: 2020-09-21 16:36:54
 * @LastEditors: guox
-->
# 制作java环境镜像

+ 拉取基础镜像
`docker pull centos:centos7`
+ 编写dockerfile文件
    ~~~conf
    FROM centos:centos7
    MAINTAINER guoxx <g15589779987@gmail.com>
    ENV JAVA_HOME /usr/local/jdk/jdk1.8.0_211
    ENV PATH $PATH:$JAVA_HOME/bin:$JAVA_HOME/jre/bin
    #ENV CLASSPATH .:$JAVA_HOME/lib:$JAVA_HOME/jre/lib
    ADD jdk-8u211-linux-x64.tar.gz  /usr/local/jdk/
    #EXPOSE 8080
    ENTRYPOINT ["java", "-jar", "/home/springboot.jar"]
    ~~~
+ 将jdk压缩包上和dockerfile文件上传到服务器
+ 制作镜像
`docker build -t='jdk8:yiyun' .`
+ 创建容器
`docker run --name GAM-registration -p 8092:8092 -v /home/DAM/GAM-registration/:/usr/local/project/ jdk8:yiyun`
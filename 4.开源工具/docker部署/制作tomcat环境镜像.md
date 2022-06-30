<!--
 * @Descripttion: 
 * @Author: guox
 * @Date: 2020-09-21 16:51:04
 * @LastEditors: guox
-->
# 制作tomcat环境镜像

+ 拉取基础镜像
`docker pull centos:centos7`
+ 编写dockerfile文件
  ~~~conf
    # 指定操作的镜像
    FROM centos:centos7
    # 维护者信息
    MAINTAINER guoxx <g15589779987@gmail.com>
    # 设定时区
    ENV TimeZone=Asia/Shanghai 
    RUN /bin/cp /usr/share/zoneinfo/Asia/Shanghai /etc/localtime && echo 'Asia/Shanghai' >/etc/timezone
    # 设置系统编码
    RUN yum install kde-l10n-Chinese -y
    RUN yum install glibc-common -y
    RUN localedef -c -f UTF-8 -i zh_CN zh_CN.utf8
    ENV LC_ALL zh_CN.UTF-8
    # 将jdk添加到镜像centos
    ADD jdk1.8.0_211  /usr/local/jdk
    # 将tomcat添加到镜像centos
    ADD apache-tomcat-8.5.42 /usr/local/tomcat
    # 添加环境变量
    ENV JAVA_HOME /usr/local/jdk
    ENV CATALINA_HOME /usr/local/tomcat
    ENV PATH $PATH:$JAVA_HOME/bin:$CATALINA_HOME/bin
    # 暴露端口
    #EXPOSE 8080
    # 启动时运行tomcat
    CMD ["/usr/local/tomcat/bin/catalina.sh","run"]
  ~~~
+ 将jdk压缩包、tomcat压缩包上传到服务器，解压后删除压缩包
+ 制作进行
`docker build -t='tomcat8-jdk8:yiyun' .`
+ 创建容器
`docker run --name GAM-BMS -p 8091:8080  -v /soft/tomcat/GAM-BMS/:/usr/local/tomcat/webapps/ -e LANG=en_US.UTF-8  tomcat:jdk8-corretto`


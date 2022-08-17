# Jenkins rpm安装
## 安装java、安装maven、安装git或svn
1. java
    `mkdir /usr/local/java/`
    `tar -zxvf jdk-8u211-linux-x64.tar.gz -C /usr/local/java/`
    `vim /etc/profile`
    ~~~ conf
    export JAVA_HOME=/usr/local/java/jdk1.8.0_211
    export JRE_HOME=${JAVA_HOME}/jre
    export CLASSPATH=.:${JAVA_HOME}/lib:${JRE_HOME}/lib
    export PATH=${JAVA_HOME}/bin:$PATH
    ~~~
    `source /etc/profile`
    `java -version`
    `ln -s /usr/local/java/jdk1.8.0_171/bin/java /usr/bin/java`
2. maven
    `mkdir /usr/local/maven/`
    `tar -zxvf apache-maven-3.6.1-bin.tar.gz -C /usr/local/maven/`
    `vim /etc/profile`
    ~~~ conf
    export MAVEN_HOME=/usr/local/maven/apache-maven-3.6.1
    export PATH=$MAVEN_HOME/bin:$PATH
    ~~~
    `source /etc/profile`
    `mvn -version`
3. git
    `yum install -y git`
## 安装jenkins
1. 添加jenkins库
    ~~~ shell
	wget -O /etc/yum.repos.d/jenkins.repo http://jenkins-ci.org/redhat/jenkins.repo
    sudo rpm --import https://pkg.jenkins.io/redhat/jenkins.io.key
    ~~~
2. 安装
	`$ yum install jenkins`
3. 安装后查看服务
	`$ chkconfig --list | grep jenkins`
## 配置
1. 修改端口
    `vim /etc/sysconfig/jenkins`
	JENKINS_PORT="8888"
2. 配置java路径，添加自己的路径
    `vim /etc/init.d/jenkins`
	candidates="/home/baseImages/tomcat8/jdk1.8.0_211/bin/java"
3. 重新加载配置文件
    `systemctl daemon-reload`
4. 启动
    `systemctl start jenkins`
5. 登录并配置插件
    
6. 修改admin默认密码
    ~~~~ shell
	find / -name config.xml
	vim /var/lib/jenkins/users/admin_1041050899378485340/config.xml
	密码改为 admin： $2a$10$OSULlMZ9FOuI7q1hkuDUCuLUxxCV8RwMNEHwyHG7SQpLGlczOPXmu
	systemctl restart jenkins
    ~~~
## 登录后配置
1. 下载插件
2. 修改配置文件，用户改成root
    `$ vim /etc/sysconfig/jenkins`

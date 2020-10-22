<!--
 * @Author: your name
 * @Date: 2020-10-22 16:47:44
 * @LastEditTime: 2020-10-22 17:44:29
 * @LastEditors: your name
 * @Description: In User Settings Edit
 * @FilePath: \github\work\jenkins\Jenkins rpm安装.md
-->
# Jenkins rpm安装
## 安装java、安装maven、安装git或svn

## 安装jenkins
1. 添加jenkins库
    ~~~ shell
	$ wget -O /etc/yum.repos.d/jenkins.repo http://jenkins-ci.org/redhat/jenkins.repo
    $ rpm --import http://pkg.jenkins-ci.org/redhat/jenkins-ci.org.key
    sudo rpm --import https://pkg.jenkins.io/redhat-stable/jenkins.io.key
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
	$ find / -name config.xml
	$ vim /var/lib/jenkins/users/admin_1041050899378485340/config.xml
	密码改为 admin： $2a$10$OSULlMZ9FOuI7q1hkuDUCuLUxxCV8RwMNEHwyHG7SQpLGlczOPXmu
	$ systemctl restart jenkins
    ~~~
## 登录后配置
1. 下载插件
2. 修改配置文件，用户改成root
    `$ vim /etc/sysconfig/jenkins`

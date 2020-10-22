<!--
 * @Author: your name
 * @Date: 2020-10-22 17:55:51
 * @LastEditTime: 2020-10-22 18:01:37
 * @LastEditors: Please set LastEditors
 * @Description: In User Settings Edit
 * @FilePath: \github\work\jenkins\window安装.md
-->
# Windows-war包本地部署

本地部署：参考博客 https://www.cnblogs.com/c9999/p/6399367.html
1. 下载war包，地址 http://mirrors.jenkins.io/war-stable/latest/jenkins.war

2. 启动jenkins java -jar jenkins.war --httpPort=8081

3. 访问jenkins http://localhost:8080/jenkins

4. 输入初始密码后，选择默认安装插件，创建用户和实例

5. "系统管理"->"全局工具配置" 配置jdk和maven

6. "系统管理"->"管理插件" 安装必要插件Maven Integration plugin和Deploy to container

7. "新建任务" 增加maven项目，配置仓库地址和账号密码，配置构建命令，添加构建后操作部署到容器并增加tomcat配置

    ![image-20201022175905210](https://raw.githubusercontent.com/guoxx-crudboy/picture/master/image-20201022175905210.png)

    ![image-20201022175828416](https://raw.githubusercontent.com/guoxx-crudboy/picture/master/image-20201022175828416.png)

    ![image-20201022175929889](https://raw.githubusercontent.com/guoxx-crudboy/picture/master/image-20201022175929889.png)

8. 配置本地tomcattomcat-user.xml
     <role rolename="admin" />
       <role rolename="admin-gui" />
       <role rolename="manager" />
       <role rolename="manager-script" />
       <role rolename="manager-gui" />
       <user username="admin" password="123456" roles="admin,manager,manager-script,manager-gui,admin-gui"/>
       用户名为admin 密码为 123456


# Windows-jar包本地部署
1.jekins初始化，通部署war
2.配置postSteps，增加windows批处理。
杀掉要正在运行的此项目进程，删掉项目jar包， 将新下载打包的jar包复制到存放项目的目录，以后台启动的方式启动服务。

~~~ shell
kill.bat 杀进程
::demo
@echo off
::延迟环境变量扩展
setlocal enabledelayedexpansion
for /f "delims=  tokens=1" %%i in ('netstat -aon ^| findstr %1') do (set a=%%i)
::判断服务是否已经启动，如果启动则杀掉进程
if defined a (taskkill /F /pid "!a:~71,5!") else (echo Service does not exist)
::等待你按任意键结束
pause>nul
::执行时后面带上端口即可


statup.bat
e:
del e:\jekins\xdata.jar
copy c:\用户我文件夹\.jekins\workspace\xdata\target\xdata.jar e:\jekins\xdata.jar
start javaw -Dhudson.util.ProcessTree.disable=true -jar e:\jekins\xdata.jar   后台运行jar
ping /n 30 127.1 >null    等待30秒

check-service.bat
@echo off
setlocal enabledelayedexpansion
for /f "delims=  tokens=1" %%i in ('netstat -aon ^| findstr %1') do (set a=%%i goto js)
:js
::判断服务是否已经启动，如果启动则返回给jenkins信号0
if defined a (exit 0) else (exit 1)
pause>nul
::在执行bat脚本的时候在命令后加上参数也就是端口即
~~~

# 部署问题

1. 启动后，输入初始密码，出现404
	地址修改为  http://localhost:8080
2. 没有在svn下载最新代码
	svn的url后面增加@head
3. 在ROOT下构建
	Context path配置为"/"
4. 输入初始密码后，选择安装插件页面显示离线
	从其他服务器copy plugin过来解决的
5. jar包部署，怎么运行项目
	基本逻辑：杀掉要正在运行的此项目进程 ，删掉项目jar包， 将新下载打包的jar包复制到存放项目的目录 ，以后台启动的方式启动服务。后面三个脚本
	最近在使用jenkins部署服务过程中遇到一个问题，那就是通过start javaw -jar 以后台启动的方式启动服务，如果服务没有成功启动，这个时候jenkins还是显示构建成功，因为对于jenkins来说这个命令本身是成功执行完成了的，jenkins不会继续判断服务是否真的启动成功构建就结束了。在启动服务之后新增一步检查服务是否真的成功启动的步骤，即通过端口查找该服务的进程，如果找到了在返回给jenkins 0表示服务正常启动，如果没找到该服务的进程。
6. jar包部署脚本，部署完成，然后将项目有停止了。
	判断是jekins结束的时候自动杀掉衍生进程，windows环境使用第一种就解决了。
	1.启动 Jenkins 的时候加上 -Dhudson.util.ProcessTree.disable=true，也就是 java -Dhudson.util.ProcessTree.disable=true -jar jenkins.war
	2.在后台进程前加上 BUILD_ID=dontKillMe,也就是 BUILD_ID=dontKillMe nohup npm start >/var/app/nohup.out 2>&1 &
7. jar使用maven构建的时候出现错误，Unknown lifecycle phase "mvn". You must specify a valid lifecycle…
    运行mvn package的路径没有指向项目文件夹，而是指向pom文件，讲mvn去掉直接写package就好了。
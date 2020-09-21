# docker与宿主机同步时间
三种修改方式

1.运行容器时，挂载在宿主机配置文件“/etc/localtime“

`docker run -it -v /etc/localtime:/etc/localtime:ro centos #:ro表示只读权限`

2.运行中容器，直接复制配置文件

`docker cp -L /usr/share/zoneinfo/Asia/Shanghai  fe51:/etc/localtime`

3.打镜像时候，直接在dockerfile中配置

~~~
ENV TimeZone=Asia/Shanghai    #添加时区环境变量，亚洲，上海
RUN ln -snf /usr/share/zoneinfo/$TimeZone /etc/localtime && echo $TimeZone > /etc/timezone   #使用软连接，并且将时区配置覆盖/etc/timezone
~~~

## 存在的问题

1.在运行容器执行同步时间后，程序时间没有同步（jvm时间没有同步）

编辑或创建/etc/timezone`vi /etc/timezone`,内容为`Asia/Shanghai`





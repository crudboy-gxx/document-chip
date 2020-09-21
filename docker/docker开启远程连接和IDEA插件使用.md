# docker开启远程连接

修改配置文件`vim /lib/systemd/system/docker.service`

修改ExecStart参数为`ExecStart=/usr/bin/dockerd -H unix:///var/run/docker.sock -H tcp://0.0.0.0:2375`

重新启动服务
~~~shell
sudo systemctl daemon-reload
sudo systemctl restart docker
~~~

验证`curl 127.0.0.1:2375/info`


# IDEA插件使用

在设置Setting中找到Plugins搜索Docker，找到Docker Integration下载重启idea，成功后在setting中可以看到Docker。点击Docker“+”新建一个Docker连接，通过tcp的方式连接到centos的docker服务。信息配置好后，idea会自动检测。然后会输出“connection successful”。

![image-20200921160402069](https://raw.githubusercontent.com/guoxx-crudboy/picture/master/image-20200921160402069.png)
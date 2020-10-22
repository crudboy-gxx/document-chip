<!--
 * @Author: your name
 * @Date: 2020-09-21 15:04:54
 * @LastEditTime: 2020-10-22 14:42:10
 * @LastEditors: your name
 * @Description: In User Settings Edit
 * @FilePath: \github\work\docker\centos7安装docker服务.md
-->
# 修改yum源

+ 下载wget 
  `yum install wget -y`
+ 备份当前yum源 
  `mv /etc/yum.repos.d /etc/yum.repos.d.backup`
+ 创建一个新的文件夹 
  `mkdir /etc/yum.repos.d`
+ 下载阿里云的yum配置 
  `wget -O /etc/yum.repos.d/CentOS-Base.repo http://mirrors.aliyun.com/repo/Centos-7.repo`
+ 重建缓存
  ~~~
  yum clean all
  yum makecache
  yum update
  ~~~

# 安装docker服务
+ 验证内核版本 
  `uname -r`
  内核版本必须高于3.10
+ 卸载旧版本 
  `sudo yum remove docker  docker-common docker-selinux docker-engine`
+ 安装需要的软件包 
  `sudo yum install -y yum-utils device-mapper-persistent-data lvm2`
  yum-util提供yum-config-manager功能，另外两个是devicemapper驱动依赖的
+ 设置yum源 
  `sudo yum-config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo`
+ 查看仓库中版本，安装17.12版本
  `yum list docker-ce --showduplicates | sort -r`
  `sudo yum install docker-ce-17.12.0.ce`
+ 设置开机启动
	~~~
  sudo systemctl start docker
  sudo systemctl enable docker
  ~~~
+ 验证安装（有client和service两部分表示docker安装启动都成功了）
  `docker version `
+ 配置加速路径 
  `vim /etc/docker/daemon.json`
  ~~~
  {"registry-mirrors": ["https://wgct39xz.mirror.aliyuncs.com"]}
  ~~~
+ 重启服务
  `sudo systemctl daemon-reload` `sudo service docker restart`
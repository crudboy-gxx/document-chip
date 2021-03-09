参考：https://blog.csdn.net/xujiamin0022016/article/details/102656119

1. 查看版本
    ``` shell
    openssl version
    ```
2. 安装依赖
    ``` shell
    yum update openssh -y
    yum install  -y gcc gcc-c++ glibc make autoconf openssl openssl-devel pcre-devel  pam-devel
    yum install  -y pam* zlib*
    ```
3. 下载压缩包并解压
    ``` shell
    cd /opt
    wget -c http://ftp.openbsd.org/pub/OpenBSD/OpenSSH/portable/openssh-8.4p1.tar.gz
    wget -c https://www.openssl.org/source/openssl-1.1.1h.tar.gz
    tar xfz openssh-8.4p1.tar.gz
    tar xfz openssl-1.1.1h.tar.gz
    ```
4. 安装openssl
	+ 授权 `chown -R root.root /opt/openssh-8.4p1`
	+ 备份
		`mv /usr/bin/openssl /usr/bin/openssl_bak`
		`mv /usr/include/openssl /usr/include/openssl_bak`
	+ 安装
		`cd /opt/openssl-1.1.1h`
		`./config shared && make && make install`
	+ 添加链接
		`ln -s /usr/local/ssl/bin/openssl /usr/bin/openssl`
		`ln -s /usr/local/ssl/include/openssl /usr/include/openssl`
		`echo "/usr/local/ssl/lib" >> /etc/ld.so.conf`
	+ 应用 `/sbin/ldconfig`
	+ 查看版本 `openssl version`
5. 安装openssh
	+ 备份
		`cp -r  /etc/ssh /etc/ssh_bak`
		`rm -rf /etc/ssh`
	+ 授权
		`cd /opt/openssh-8.4p1`
		`chown -R root.root /opt/openssh-8.4p1`
	+ 编译安装openssh8.1
		`./configure --prefix=/usr/ --sysconfdir=/etc/ssh  --with-openssl-includes=/usr/local/ssl/include --with-ssl-dir=/usr/local/ssl   --with-zlib   --with-md5-passwords   --with-pam --without-openssl-header-check  && make && make install`
	+ 修改/etc/ssh/sshd_config
		`vim /etc/ssh/sshd_config`
		``` shell
		PermitRootLogin yes 
		PubkeyAuthentication yes 
		PasswordAuthentication yes
		```
	+ 设置开机自启 -- 当前不可用
		``` shell
        cp -a contrib/redhat/sshd.init /etc/init.d/sshd
		cp -a contrib/redhat/sshd.pam /etc/pam.d/sshd.pam
		chmod +x /etc/init.d/sshd
		chkconfig --add sshd
		systemctl enable sshd
		mv  /usr/lib/systemd/system/sshd.service  /opt/
		mv  /usr/lib/systemd/system/sshd.socket  /opt/
		chkconfig sshd on
        ```
		`service sshd restart` -- 新的设置开机启动命令
		`chkconfig --list | grep sshd` -- 确认开机启动加入正常

	+ 重启
		`service sshd restart` -- 当前不可用
		
		`/etc/init.d/sshd start` -- 新的启动命令
	+ 查看版本 
        `ssh -V`

	参考地址：https://blog.51cto.com/rongshu/2576850

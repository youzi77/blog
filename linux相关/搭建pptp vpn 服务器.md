**服务器系统版本：**
CentOS 8  x86_64 (64bit)

### 准备工作：
​		搭建PPTP服务和搭建L2TP/IPSec服务均需要设置内核支持转发

- 编辑sysctl.conf

  ``` 
  vim /etc/sysctl.conf
  ```

- 在末尾添加

  ``` 
  net.ipv4.ip_forward = 1
  ```

- 保存并退出，执行以下命令使其生效

  ``` 
  sysctl -p
  ```

  


###  搭建PPTP服务

- 检测PPP服务是否开启

  ```
   cat /dev/ppp
  ```

  结果显示：

  ``` 
  cat: /dev/ppp: No such file or directory 或者 cat: /dev/ppp: No such device or address
  ```


  可以继续

- 安装PPP、PPTPD、Iptables组件

  ``` yum install -y ppp iptables pptpd
  安装之前，可以更新yum源
  ```

- 编辑配置文件

  1. 编辑pptpd.conf

     ```
     vim /etc/pptpd.conf
     ```

  2. 添加以下字段并保存退出

     ``` 
     localip 192.168.9.1  指定一个内网网段可理解为主路由（VPN网关地址）
     remoteip 192.168.9.11-30 vpn分配网段（分配给客户端IP地址范围）
     ```

  

- 编辑options.pptpd

  ``` 
  vim /etc/ppp/options.pptpd
  ```

  找到ms-dns，去掉#号注释修改为

  ``` 
  ms-dns 8.8.8.8
  ms-dns 8.8.4.4
  ```

  保存并退出

- 编辑chap-secrets设置VPN用户名密码

  ``` 
  vim /etc/ppp/chap-secrets
  ```

  添加一行

  ``` 
  用户名	* 密码	*
  test pptpd 123456 *
  test2 * 123456 *
  ```

  注意：用户名密码区分大小写

### 防火墙配置及设置nat转发

 1. 启动防火墙

    ``` 
    systemctl start firewalld
    ```

2. 启用NAT转发（必须启用此功能）

   ```
   firewall-cmd --permanent --add-masquerade
   ```

3. 开启47及1723端口

    ``` 
    firewall-cmd --permanent --zone=public --add-port=47/tcp
    firewall-cmd --permanent --zone=public --add-port=1723/tcp
    ```

4. 设置规则

   ```
   firewall-cmd --permanent --direct --add-rule ipv4 filter INPUT 0 -p gre -j ACCEPT
   firewall-cmd --permanent --direct --add-rule ipv4 filter OUTPUT 0 -p gre -j ACCEPT
   firewall-cmd --permanent --direct --add-rule ipv4 filter FORWARD 0 -i ppp+ -o eth0 -j ACCEPT
   firewall-cmd --permanent --direct --add-rule ipv4 filter FORWARD 0 -i eth0 -o ppp+ -j ACCEPT
   firewall-cmd --permanent --direct --passthrough ipv4 -t nat -I POSTROUTING -o eth0 -j MASQUERADE -s 内网网段(vpn网管你地址)
   例：
   firewall-cmd --permanent --direct --passthrough ipv4 -t nat -I POSTROUTING -o eth0 -j MASQUERADE -s 192.168.9.1/24
   ```

5. 重启防火墙

   ``` 
   firewall-cmd --reload
   ```

### 启动PPTP服务

- 启动

  ```
  systemctl start pptpd
  ```

- 查看服务状态

  ``` 
  systemctl status pptpd
  ```

- 设置pptpd服务开机自启

  ``` 
  systemctl enable pptpd
  ```


至此，PPTP服务已搭建完成，可以使用Windows或Mac通过VPN连接。

## 搭建L2TP服务

一、安装EPEL源
	yum install -y epel-release
1
二、安装Xl2TPD等服务
	yum install -y xl2tpd libreswan lsof
1
三、编辑配置文件
1、编辑xl2tpd.conf

	vim /etc/xl2tpd/xl2tpd.conf
1.修改以下内容

	[lns default]
	ip range = VPN分配的网段
	local ip = 本机内网IP
	require chap = yes
	refuse pap = yes
	require authentication = yes
	name = LinuxVPNserver
	ppp debug = yes
	pppoptfile = /etc/ppp/options.xl2tpd
	length bit = yes

2、编辑options.xl2tpd

	vim /etc/ppp/options.xl2tpd

修改以下内容

	ipcp-accept-local
	ipcp-accept-remote
	ms-dns  114.114.114.114
	ms-dns  223.5.5.5
	# ms-dns  192.168.1.1
	# ms-dns  192.168.1.3
	# ms-wins 192.168.1.2
	# ms-wins 192.168.1.4
	noccp
	auth
	#crtscts
	idle 1800
	mtu 1410
	mru 1410
	nodefaultroute
	debug
	#lock
	proxyarp
	connect-delay 5000
	3、编辑l2tp-ipsec.conf（没有则新建）
	vim /etc/ipsec.d/l2tp-ipsec.conf

添加或修改以下内容

``` config setup
    nat_traversal=yes
    virtual_private=%v4:10.0.0.0/8,%v4:192.168.0.0/16,%v4:172.16.0.0/12
    oe=off
    protostack=netkey
    conn L2TP-PSK-NAT
    rightsubnet=vhost:%priv
    also=L2TP-PSK-noNAT
    conn L2TP-PSK-noNAT
    authby=secret
    pfs=no
    auto=add
    keyingtries=3
    rekey=no
    ikelifetime=8h
    keylife=1h
    type=transport
    left=172.16.6.5  #本机内网IP
    leftprotoport=17/1701
    right=%any
    rightprotoport=17/%any
```


4、编辑chap-secrets设置VPN用户名密码
这个配置文件和PPTPD的用户名密码配件文件共用，参考PPTP的设置

四、防火墙设置
1、允许ipsec服务

	firewall-cmd --permanent --add-service=ipsec

2、允许1701、4500端口

firewall-cmd --permanent --add-port=1701/udp
firewall-cmd --permanent --add-port=4500/udp
3、启用NAT转发
搭建PPTP时，如果已开启可忽略

firewall-cmd --permanent --add-masquerade
4、重启防火墙

	firewall-cmd --reload

五、修改内核参数
	vim /etc/sysctl.conf
添加以下内容

net.ipv4.conf.all.accept_redirects = 0
net.ipv4.conf.all.rp_filter = 0
net.ipv4.conf.all.send_redirects = 0
net.ipv4.conf.default.accept_redirects = 0
net.ipv4.conf.default.rp_filter = 0
net.ipv4.conf.default.send_redirects = 0
net.ipv4.conf.eth0.accept_redirects = 0
net.ipv4.conf.eth0.rp_filter = 0
net.ipv4.conf.eth0.send_redirects = 0
net.ipv4.conf.eth1.accept_redirects = 0
net.ipv4.conf.eth1.rp_filter = 0
net.ipv4.conf.eth1.send_redirects = 0
net.ipv4.conf.eth2.accept_redirects = 0
net.ipv4.conf.eth2.rp_filter = 0
net.ipv4.conf.eth2.send_redirects = 0
net.ipv4.conf.ip_vti0.accept_redirects = 0
net.ipv4.conf.ip_vti0.rp_filter = 0
net.ipv4.conf.ip_vti0.send_redirects = 0
net.ipv4.conf.lo.accept_redirects = 0
net.ipv4.conf.lo.rp_filter = 0
net.ipv4.conf.lo.send_redirects = 0
net.ipv4.conf.ppp0.accept_redirects = 0
net.ipv4.conf.ppp0.rp_filter = 0
net.ipv4.conf.ppp0.send_redirects = 0

使其修改生效

	sysctl -p

六、启动ipsec及xl2tp服务
1、启动ipsec服务

	systemctl start ipsec

2、检查ipsec配置

	ipsec verify

3、设置ipsec开机自启

	systemctl enable ipsec

4、启动xl2tp服务

	systemctl start xl2tp

5、设置xl2tp开机自启

	systemctl enable xl2tpd


原文链接：https://blog.csdn.net/iruier_/article/details/104294045
##### java

切换java：alternatives --config java

安装jdk：yum install java-1.8.0-openjdk-devel.x86_64

##### 网络相关

查看ip地址, ip addr; 以前是ifconfig

##
1、关闭防火墙：sudo systemctl stop firewalld.service
2、关闭开机启动：sudo systemctl disable firewalld.service
##

ifconfig -a 看那块网卡没有加，这里比如eth1
/etc/sysconfig/network-scripts
[root@pps network-.s]# cat ifcfg-eth1
DEVICE=eth1
ONBOOT=yes
BOOTPROTO=static
HWADDR=00:0c:29:5c:c1:7f
IPADDR=172.18.70.50
NETMASK=255.255.255.0
GATEWAY=172.18.70.1
sudo vi /etc/resolv.conf
nameserver 180.76.76.76
nameserver 61.139.2.69

3、重启eth1
[root@pps network-.s]# ip link set dev eth0 down
[root@pps network-.s]# ip link set dev eth0 up

#### 新旧命令对照
1. 查看 IP 地址和数据统计
ifconfig 版本：
ifconfig：显示 up 状态的端口信息，包括 ip、mac、统计等。
ifconfig -a：显示所有状态的端口信息，包括 ip、mac、统计等。
ip 版本：
ip addr（简写成 ip a）:显示所有端口信息，包括 ip、mac、up/down 等。
ip -4 a：仅显示带 ipv4 地址的端口信息。
ip -s link：显示所有端口统计信息。
ip -s link ls eth0：显示 eth0 端口的统计信息。
ip -s -s link ls eth0：更详细地显示 error 信息。
2. 添加/删除端口的 ip 地址
ifconfig 版本：
ifconfig eth0 192.168.120.56 netmask 255.255.255.0：临时添加 eth0 的 ip 地址。
ip版本：
ip addr add 192.168.2.199/24 dev eth0：临时添加 eth0 的 ip 地址。
ip addr del 192.168.2.199/24 dev eth0：删除 eth0 的 ip 地址。
3. UP/DOWN 端口
ifconfig 版本：
ifconfig eth0 up：将端口 eth0 启动。
ifconfig eth0 down：将端口 eth0 关闭。
ifup/ifdown 版本：
ifup ifcfg-eth0：将端口 eth0 启动。
ifdown ifcfg-eth0：将端口 eth0 关闭。
ip 版本：
ip link set dev eth0 up：将端口 eth0 启动（无需 ifcfg-eth0 文件存在）。
ip link set dev eth0 down：将端口 eth0 启动（无需 ifcfg-eth0 文件存在）。
4. 路由配置
route 版本：
route -n：查看路由信息
route add default gw 192.168.1.1 eth0：添加默认网关路由
route add -net 192.168.1.0 netmask 255.255.255.0 dev eth0：添加到网络的路由
route add -host 10.20.30.148 gw 10.20.30.40：添加到主机的路由
ip 版本：
ip route：查看路由信息
ip route add default via 192.168.1.1：添加默认网关路由
ip route add 192.168.0.0/24 via 192.168.1.2：添加到网络的路由
ip route add 192.168.1.3 via 172.16.0.1 dev eth0：添加到主机的路由

#### 增加esxi中的虚拟机的磁盘空间

https://ma.ttias.be/increase-a-vmware-disk-size-vmdk-formatted-as-linux-lvm-without-rebooting/

这一步, lvextend /dev/VolGroup00/LogVol00 /dev/sda3 ==> lvextend /dev/centos/root   /dev/sda3

﻿##### 安装nginx

﻿yum install epel-release
﻿yum -y install nginx
﻿service nginx start

使用特定端口, nginx: [emerg] bind() to failed (13: Permission denied), 报错

semanage port -l | grep http_port_t
semanage port -a -t http_port_t  -p tcp 8090

##### yum相关

**yum update, 中间退出了，总是报告连接复位，yum clean all. 正常了。
**yum -ivh?
**yum update git, 更新git到最新版本

##### Yum update, 报告这个错误"Recv failure: Connection reset by peer"

安装的时候,一定要选择E100类型的网卡.
在安全策略中,选择off, 关闭
然后就对了, 不知道为啥不对. 以前总要报这个错误

##### rpm -ivh -replacefiles *.rpm, 报告冲突，强制更新

tar -zcvf archive-name.tar.gz directory-name 加压
tar -zxvf prog-1-jan-2005.tar.gz 解压

##### python相关

yum?install?epel-release
yum install python-pip

##### visudo，加入sudoers

usermod -aG wheel tong,     <username>

打开wheel的注释

##### 配置
	"/etc/profile"此文件为系统的每个用户设置环境信息，当用户第一次登录时，该文件被执行。并从"/etc/profile.d"目录的配置文件中搜集shell的设置。所以如果你有对/etc/profile有修改的话必须得重启你的修改才会生效，此修改对每个用户都生效。
	"/etc/bashrc"为每一个运行bash shell的用户执行此文件。当bash shell被打开时，该文件被读取。如果你想对所有的使用bash的用户修改某个配置并在以后打开的bash都生效的话可以修改这个文件，修改这个文件不用重启，重新打开一个bash即可生效。
	"~/.bash_profile"每个用户都可使用该文件输入专用于自己使用的shell信息，当用户登录时，该文件仅仅执行一次！默认情况下，它设置一些环境变量，执行用户的.bashrc文件。此文件类似于/etc/profile，也是需要重启才会生效，/etc/profile对所有用户生效，~/.bash_profile只对当前用户生效。
	"~/.bashrc"该文件包含专用于你的bash shell的bash信息，当登录时以及每次打开新的shell时，该文件被读取。（每个用户都有一个.bashrc文件，在用户目录下）。此文件类似于/etc/bashrc，不需要重启生效，重新打开一个bash即可生效， /etc/bashrc对所有用户新打开的bash都生效，但~/.bashrc只对当前用户新打开的bash生效。
	"~/.bash_logout"当每次退出系统（退出bash shell）时，执行该文件。
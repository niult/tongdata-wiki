# （四）大数据NiFi--NiFi单节点安装

[![Lansonli](https://picx.zhimg.com/v2-a21621b28f2fb5ef77d23387fdced39e_l.jpg?source=172ae18b)](https://www.zhihu.com/people/lanson-92-34)

[Lansonli](https://www.zhihu.com/people/lanson-92-34)



## **NiFi单节点安装**

## **一、介绍与下载**

Apache NiFi可以基于Linux和Window安装，这里建议基于Linux安装。安装NiFi的节点需要安装JDK8,NiFi0.x版本需要JDK7。NiFi安装可以单节点安装，也可以分布式安装。我们这里安装NiFi的1.13版本，需要JDK8。

NiFI下载官网地址：**Apache NiFi Downloads[1]**

![img](https://pic2.zhimg.com/80/v2-bcee8a44d1f61f3891d40669ec82b40d_1440w.webp)



## **二、单节点安装**

### **1、将下载好的NiFi安装包上传解压**

```text
#这里将NiFi安装包上传到node5节点上
[root@node5 software]# tar -zxvf ./nifi-1.13.0-bin.tar.gz 
nifi-1.13.0/README
nifi-1.13.0/LICENSE
... ....
```



### **2、修改WebUI端口**

NiFi启动后，默认的WebUI端口为8080,为了防止和其他服务端口冲突，可以配置 $NiFi_HOME/conf/nifi.properties，修改WebUI端口，也可以不配置。

```text
#vim /software/nifi-1.13.0/conf/nifi.properties 144行
nifi.web.http.host=192.168.179.8
nifi.web.http.port=8989 
```



### **3、启动关闭NiFi**

```text
#进入 $NiFi_HOME/bin目录下，启动NiFi服务
[root@node5 bin]# ./nifi.sh start

#查看NiFi进程
[root@node5 logs]# jps
8025 NiFi
8537 Jps
8011 RunNiFi

#关闭NiFi服务
[root@node5 bin]# ./nifi.sh stop
```



以上启动NiFi之后，启动日志在 $NiFi_HOME/logs/nifi-app.log文件中，需要等待一会NiFi才能完全启动成功。当日志中出现下图标志时，NiFi启动成功：

![img](https://pic3.zhimg.com/80/v2-94c67c7700b648c832191798f8b3dce2_1440w.webp)

### **4、将NiFi作为系统服务，方便启动和关闭**

也可以将NiFi作为系统服务启动，需要执行命令:$NiFi_HOME/bin/nifi.sh install ,这样就以默认名称“nifi”安装服务，这样就可以使用命令“service nifi start/stop”来启动关闭服务。

```text
#安装系统服务nifi
[root@node5 ~]# cd /software/nifi-1.13.0/bin/
[root@node5 bin]# ./nifi.sh install
Service nifi installed

#启动nifi服务
[root@node5 bin]# service nifi start

#关闭nifi服务
[root@node5 bin]# service nifi stop
```



### **5、访问WebUI界面**

**[http://node5:8989/nifi](https://link.zhihu.com/?target=http%3A//node5%3A8989/nifi)[2]**

![img](https://pic2.zhimg.com/80/v2-d9a05ebbed3054a7361bcbf049d4c16d_1440w.webp)

### **参考资料**

[1] Apache NiFi Downloads: *[http://nifi.apache.org/download.html](https://link.zhihu.com/?target=http%3A//nifi.apache.org/download.html)*

[2] [http://node5:8989/nifi:](https://link.zhihu.com/?target=http%3A//node5%3A8989/nifi%3A) *[http://node5:8989/nifi](https://link.zhihu.com/?target=http%3A//node5%3A8989/nifi)*

编辑于 2023-01-20 11:30・IP 属地广东
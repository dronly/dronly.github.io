---
layout: post
title: hive 源码开发调试环境搭建
categories: hive
header-style: text
tags:
- 大数据
- Hive
---
# hive 源码开发调试环境搭建

<a name="vd5Yf"></a>
# 环境搭建
下载软件包，这里以 Hadoop2.7.3 与 hive 1.2.1 为例<br />版本包地址：

- hadoop 2.7.3： [http://archive.apache.org/dist/hadoop/common/hadoop-2.7.3/](http://archive.apache.org/dist/hadoop/common/hadoop-2.7.3/)
- hive 1.2.1  ：[http://archive.apache.org/dist/hive/hive-1.2.1/](http://archive.apache.org/dist/hive/hive-1.2.1/)

<a name="CMTkl"></a>
## 单节点 Hadoop 搭建
<a name="mpMIi"></a>
### 环境搭建
<a name="sQh66"></a>
#### 1. 解压 hadoop 软件包并进入配置文件目录
 `tar xvf hadoop-2.7.3.tar.gz; cd hadoop-2.7.3/etc/hadoop`
<a name="N7svz"></a>
#### 2. 修改配置文件 core-site.xml，hdfs-site.xml， mapred-site.xml

```xml
<! core-site.xml>

<configuration>

     <property>
        <name>hadoop.tmp.dir</name>
        <value>/hadoop</value>
     </property>
     <property>
        <name>dfs.name.dir</name>
        <value>/hadoop/name</value>
     </property>
     <property>
        <name>fs.default.name</name>
        <value>hdfs://master:9000</value>
     </property>
</configuration>

<! hdfs-site.xml>
<configuration>
    <property>
        <name>dfs.namenode.name.dir</name>
        <value>/Users/mayanbo/hadoop/namenode</value>
    </property>
    <property>
        <name>dfs.data.dir</name>
        <value>/Users/mayanbo/hadoop/data</value>
    </property>
    <property>
        <name>dfs.http.address</name>
        <value>0.0.0.0:50070</value>
    </property>
</configuration>

<! mapred-site.xml>
<configuration>

     <property>
        <name>mapred.job.tracker</name>
        <value>master:9001</value>
     </property>

</configuration>
```


<a name="nd4xU"></a>
#### 3. 配置 本地免密登录 即 ssh localhost 可以登录

```xml


1。 设置自己的mac允许远程登录：
	首先我们打开系统偏好设置–>共享
	我们将远程登录、所有用户勾选

2. 设置免密码
cat ~/.ssh/id_rsa.pub >> ~/.ssh/authorized_keys
chmod og-wx ~/.ssh/authorized_keys
chmod 750 $HOME

执行 ssh localhost 测试
```
<a name="fXKYO"></a>
#### 4. 启动 hadoop   ~/hadoop/hadoop-2.7.3/sbin/start-all.sh

<a name="OQEO7"></a>
### 验证安装成功
在 hdfs 创建文件夹 hadoop fs -mkdir -p /data/input  hadoop fs -mkdir -p /data/out<br />上传文本文件<br />`hadoop fs -put a.txt /data/input`<br />执行 wordcount <br />`hadoop jar ~/hadoop/hadoop-2.7.3/share/hadoop/mapreduce/hadoop-mapreduce-examples-2.7.3.jar wordcount /data/input/a.txt /data/out/my_wordcont`

[http://127.0.0.1:8088](http://127.0.0.1:8088)  访问 mapreduce 提供的任务查看页面[http://127.0.0.1:50070](http://127.0.0.1:50070/)  访问hadoop提供的web页面，通过**Browse the system**，可以查看hdfs中的文件。


参考资料<br />Hadoop单节点配置 [https://blog.csdn.net/qq_30450401/article/details/89489823](https://blog.csdn.net/qq_30450401/article/details/89489823)<br />运行hadoop worldcount [https://blog.csdn.net/alexwym/article/details/82497582](https://blog.csdn.net/alexwym/article/details/82497582)<br />


<a name="ynwET"></a>
## 单节点 Hive 搭建
<a name="HP4IM"></a>
### 环境搭建
参考<br /> [https://blog.csdn.net/qq_30450401/article/details/89489823](https://blog.csdn.net/qq_30450401/article/details/89489823)<br />[https://blog.csdn.net/iKuboo/article/details/100187487](https://blog.csdn.net/iKuboo/article/details/100187487)
<a name="v4WnY"></a>
#### 1. 解压安装包
 tar -zxvf apache-hive-1.2.1-bin.tar.gz 
<a name="Ta51k"></a>
#### 2. 修改配置文件
进入到解压的 hive 路径；复制 hive 模板配置文件

```shell
$ cd apache-hive-1.2.1-bin
$ cp conf/hive-env.sh.template conf/hive-env.sh
$ cp conf/hive-default.xml.template conf/hive-site.xml
```

配置文件修改内容 conf/hive-site.xml 
```xml

<! 配置文件 conf/hive-site.xml>

<?xml version="1.0"?>
<?xml-stylesheet type="text/xsl" href="configuration.xsl"?>
 
<configuration>
  
  <! 设置临时目录文件夹 >
  <property>
   <name>system:java.io.tmpdir</name>
   <value>/Users/root/hadoop/tmp</value>
  </property>
  <property>
     <name>system:user.name</name>
     <value>hive</value>
  </property>
  
  
        <!-- （mysql地址localhost） -->
        <property>
                <name>javax.jdo.option.ConnectionURL</name>
                <value>jdbc:mysql://localhost:3306/hive</value>
        </property>
        <!-- （mysql的驱动） -->
        <property>
                <name>javax.jdo.option.ConnectionDriverName</name>
                <value>com.mysql.jdbc.Driver</value>
        </property>
        <!-- （用户名） -->
        <property>
                <name>javax.jdo.option.ConnectionUserName</name>
                <value>root</value>
        </property>
        <!-- （密码） -->
        <property>
                <name>javax.jdo.option.ConnectionPassword</name>
                <value>root</value>
        </property>
        <property>
                <name>hive.metastore.schema.verification</name>
                <value>false</value>
        </property>


```
配置文件 conf/hive-env.sh
```xml
# 添加 Hadoop Home 目录
HADOOP_HOME=/Users/mayanbo/hadoop/hadoop-2.7.3
```

<a name="Y01Zl"></a>
#### 3. mysql jdbc 驱动
负责 mysql jdbc 驱动到 hive的 lib 目录下。<br />下载地址 [https://dev.mysql.com/downloads/connector/j/](https://dev.mysql.com/downloads/connector/j/)<br />也可以通过 maven 仓库下载好后复制
<a name="TXFCz"></a>
#### 4. 初始化 hive metastore 元数据库
`./bin/schematool -dbType mysql -initSchema`<br />

<a name="k6WoV"></a>
#### 5. 启动 hive
`**./bin/hive**`<br />启动 hive  后，就可以直接在命令行执行 DDL，和DML语句了<br />注意这种方式启动hive只适用于测试等，在退出终端后hive 服务也会停止。且无法通过java等客户端连接hive。<br />

<a name="qwLlQ"></a>
#### 6. debug
开启 hive debug 日志，日志会输出到命令行。<br />hive --hiveconf hive.root.logger=DEBUG,console<br />开启源码调试 hive --debug

<a name="XmSQ8"></a>
# 源码调试

<a name="iGIRW"></a>
#### 1. 下载 hive 源码 
（下载的源码要与安装的单节点 hive 版本一致）<br />下载好后，解压，并进行编译 [http://archive.apache.org/dist/hive/hive-1.2.1/](http://archive.apache.org/dist/hive/hive-1.2.1/)

```shell
$ tar xvf apache-hive-1.2.1-src.tar.gz
$ cd apache-hive-1.2.1-src
$ mvn clean package -Phadoop-2 -DskipTests -Pdist
```
![image.png](https://cdn.nlark.com/yuque/0/2019/png/134761/1572416635887-827ca32f-6b73-42de-8831-125cee89a921.png#align=left&display=inline&height=420&name=image.png&originHeight=840&originWidth=898&search=&size=778603&status=done&width=449)
<a name="THEvg"></a>
#### 2. 开启 hive 远程调试
命令行执行`hive –debug`启动hive远程调试模式，这个在 hive的0.8 以上版本才支持此功能。

```bash
$ hive --debug
Listening for transport dt_socket at address: 8000

```

![image.png](https://cdn.nlark.com/yuque/0/2019/png/134761/1572417847084-823cd6c6-ec82-418b-82bd-9e24feb8d1d3.png#align=left&display=inline&height=30&name=image.png&originHeight=60&originWidth=478&search=&size=16191&status=done&width=239)

<a name="r9R59"></a>
#### 3. 用 idea 打开 hive 源码项目，设置 remote
![image.png](https://cdn.nlark.com/yuque/0/2019/png/134761/1572416988290-386cb56e-4100-4bb7-854f-08d4e95f6edb.png#align=left&display=inline&height=338&name=image.png&originHeight=676&originWidth=1071&search=&size=358654&status=done&width=535.5)
<a name="7W4y5"></a>
#### 3. 设置断点并debug
在启动类 /Users/lcc/Downloads/source/hive-2.2.0/cli/src/java/org/apache/hadoop/hive/cli/CliDriver.java<br />的 main 方法打断点。然后执行 debug。点击完成后，可以看到程序已经运行到断点处了。就可以开启调试了

![image.png](https://cdn.nlark.com/yuque/0/2019/png/134761/1572418033589-1c8dce03-6c76-4503-bd4e-d3338872ce1f.png#align=left&display=inline&height=249&name=image.png&originHeight=498&originWidth=819&search=&size=253951&status=done&width=409.5)

参考： IDEA hive 源码调试 [https://blog.csdn.net/qq_21383435/article/details/81137805](https://blog.csdn.net/qq_21383435/article/details/81137805)

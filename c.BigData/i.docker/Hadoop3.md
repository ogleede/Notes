# Hadoop3

**尚未成功的docker搭建hadoop集群**

### 集群分发脚本

```shell
sudo ./bin/xsync /etc/profile.d/my_env.sh				
给root以绝对路径来执行 不在其目录下的分发脚本
```

### ssh免密登录

公私钥，公钥发送，然后存储在授权文件夹中。

```shell
cd $HOME
ll -a
cd .ssh/
ssh-keygen -t rsa
ssh-copy-id hadoop2

root用户再单独配置一遍
```

### 集群配置

* NameNode和SecondaryNameNode不要安装在同一台服务器，都比较消耗内存
* ResourceManager也很消耗内存，不要和NameNode、SecondayNameNode配置在同一台机器上

|      | hadoop1              | hadoop2                        | hadoop3                       |
| ---- | -------------------- | ------------------------------ | ----------------------------- |
| HDFS | NameNode<br>DataNode | DataNode                       | SecondaryNameNode<br>DataNode |
| YARN | <br>NodeManager      | ResourceManager<br>NodeManager | <br>NodeManager               |

* hadoop配置文件分两类：默认配置文件&自定义配置文件，只有用户想修改某一默认配置值时，才需要修改自定义配置文件，更改相应属性值。

* 默认配置文件：

* | 要获取的默认文件     | 文件存放在hadoop的jar包中的位置                           |
    | -------------------- | --------------------------------------------------------- |
    | core-default.xml     | hadoop-common-3.1.3.jar/core-default.xml                  |
    | [hdfs-default.xml]   | hadoop-hdfs-3.1.3.jar/hdfs-default.xml                    |
    | [yarn-default.xml]   | hadoop-yarn-common-3.1.3.jar/yarn-default.xml             |
    | [mapred-default.xml] | hadoop-mapreduce-client-core-3.1.3.jar/mapred-default.xml |

* 自定义配置文件：

* **core-site.xml**、**hdfs-site.xml**、**yarn-site.xml**、**mapred-site.xml**四个配置文件存放在$HADOOP_HOME/etc/hadoop这个路径上，用户可以根据项目需求重新进行修改配置。

* 找默认配置做更改。



### 配置workers

```shell
vim /opt/module/hadoop~~~/etc/hadoop/workers

增加节点，结尾不许有空格，文件中不允许有空行
```



### 启动集群

* 第一次启动要在主节点初始化NameNode

    ```shell
    hdfs namenode -format
    sbin/start-dfs.sh      启动集群
    jps
    ```

    

    

### docker端口映射：

hadoop1：namenode-8020、namenodeweb-9870、历史服务器-10020、历史服务器web-19888

hadoop2：yarn-8088、

hadoop3：2ndnamenodeweb-9868、![截屏2021-10-06 上午9.47.07](Hadoop3.assets/%E6%88%AA%E5%B1%8F2021-10-06%20%E4%B8%8A%E5%8D%889.47.07-6912430.png)



![截屏2021-10-06 上午10.21.32](Hadoop3.assets/%E6%88%AA%E5%B1%8F2021-10-06%20%E4%B8%8A%E5%8D%8810.21.32.png)

​	

```shell
docker run -itd --privileged -v /Users/ogleede/Desktop/hadoop1:/dataV --name hadoop1 --hostname hadoop1 --net bridge -p 8020:8020 -p 9870:9870 -p 10020:10020 -p 19888:19888 -p 18021:22 --add-host hadoop2:172.17.0.3 --add-host hadoop3:172.17.0.4 hadoop1:toPortMap /usr/sbin/init

docker run -itd --privileged --name hadoop2 --hostname hadoop2 --net bridge -p 8088:8088 -p 18022:22 --add-host hadoop1:172.17.0.2 --add-host hadoop3:172.17.0.4 hadoop2:toPortMap /usr/sbin/init

docker run -itd --privileged --name hadoop3 --hostname hadoop3 --net bridge -p 9868:9868 -p 18023:22 --add-host hadoop1:172.17.0.2 --add-host hadoop2:172.17.0.3 hadoop3:toPortMap /usr/sbin/init
```


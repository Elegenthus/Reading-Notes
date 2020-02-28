### Hadoop_install

* 安装配置步骤
    * 使用homebrew安装 
        * `brew install hadoop`
        * hadoop将按照预定目录安装在`/usr/local/Cellar/hadoop/3.1.2/`
    * 环境变量配置
        * `vim ~/.bash_profile`
        * 增加
            * `export HADOOP_HOME=/usr/local/Cellar/hadoop/3.1.2/libexec`
            * `export PATH=$PATH:$HADOOP_HOME/bin`
        * source ~/.bash_profile
        * 运行`hadoop version`成功即证明环境变量配置成功
    * 修改hadoop-env.sh文件
        * 使用`/usr/libexec/java_home`查看JAVA_HOME
        * 修改JAVA_HOME、HADOOP_HOME和HADOOP_CONF_DIR
    * 修改core-site.xml文件
        ```
        <configuration>
            <property>
                <name>fs.defaultFS</name>
                <value>hdfs://localhost:9000</value>
            </property>
            <property>
                <!-- 创建一个文件夹存放Hadoop产生的临时文件  -->
                <name>hadoop.tmp.dir</name>
                <value>/usr/local/Cellar/hadoop/3.1.2/tmp</value>
            </property>
        </configuration>
        ```
    * 修改hdfs-site.xml
        ```
        <configuration>
            <property>
                <name>dfs.replication</name>
                <value>1</value>
            </property>
            <!--不是root用户也可以写文件到hdfs-->
            <property>
                <name>dfs.permissions</name>
                <value>false</value>    <!--关闭防火墙-->
            </property>
                <!--把路径换成本地的name坐在位置-->
            <property>
                <name>dfs.namenode.name.dir</name>
                <value>/usr/local/Cellar/hadoop/3.1.2/tmp/dfs/name</value>
            </property>
                <!--在本地新建一个存放hadoop数据的文件夹，然后将路径在这里配置一下-->
            <property>
                <name>dfs.datanode.data.dir</name>
                <value>/usr/local/Cellar/hadoop/3.1.2/tmp/dfs/data</value>
            </property>
            <property>
                <name>fs.permissions.umask-mode</name>
                <value>002</value>
            </property>
            <property>
                <name>dfs.permissions.superusergroup</name>
                <value>hdfs</value>
                </property>
        </configuration>
        ```
    * 修改mapred-site.xml文件
    ```
    <configuration>
        <property>
        <!--指定mapreduce运行在yarn上-->
            <name>mapreduce.framework.name</name>
            <value>yarn</value>
        </property>
    </configuration>
    ```
    * 修改yarn-site.xml文件
    ```
    <configuration>
            <property>
            <name>yarn.nodemanager.aux-services</name>
            <value>mapreduce_shuffle</value>
        </property>
        <property>
            <name>yarn.resourcemanager.address</name>
            <value>localhost:9000</value>
        </property>
    </configuration>
    ```
    * 如果出现permission denied错误
        * 启动ssh免密登录
            * cat ~/.ssh/id_rsa.pub >> ~/.ssh/authorized_keys
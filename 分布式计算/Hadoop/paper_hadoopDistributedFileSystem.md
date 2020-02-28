## The Hadoop Distributed File System

* HDFS is the file system component of Hadoop. While the interface to HDFS is patterned after the UNIX file system, faithfulness to standard was sacrificed in favour of improved performance for the application at hand.
    * 译：HDFS是Hadoop的文件系统组件。虽然在UNIX文件系统之后对HDFS的接口进行了模块化，但是为了提高手头程序应用的性能，降低了对标准的规范化


* A. NameNode
    * 文件被分成很多block，NameNode存储命名空间树以及映射到DataNode的文件块。
    * 客户端想要读写文件都需要通过访问NameNode来找到DataNode进行，写文件时，每一个节点要向NameNode申请三份DataNode来存放文件
    * image:包含名称系统元数据的索引节点数据和每个文件的块列表称之为映像
        * The inode data and the list of blocks belonging to each file comprise the metadata of the name system called the image
    * checkpoint:存储在本地主机的本机文件系统中image（映像）的持久记录
        * The persistent record of the image stored in the local host’s native files system is called a checkpoint. 
    * journal: image的修改日志
        * The NameNode also stores the modification log of the image called the journal in the local host’s na- tive file system. 
        * NameNode还将名为journal的image的修改日志存储在本地主机的本机文件系统中
* B. DataNode
    * 每一个DataNode的每个块副本都由本地主机的本机文件系统的两个文件表示
        * 第一个文件包含数据本身，其大小是等于块的大小的，不像传统文件一样需要使用额外的存储空间记录名义上的块大小，比如说，如果块是半满的，那么在本地也只需要整个块一半的空间。
        * 第二个文件包含块数据的校验以及块的`generation stamp`
    * 启动时每次DataNode都会连接NameNode并进行一次握手，为了验证命名空间ID和DataNode的软件版本，如果不匹配，DataNode就此关闭
    * 使用命名空间ID（namespace ID）来保持一个文件系统的完整性
    * 一个DataNode通过向NameNode发送块报告来确认自己的块副本，一个块报告包括，块id，`generation stamp`，服务器托管的块副本的长度
    * 在DataNode注册之后，会马上发送一个块报告给NameNode，随后每一小时发送一次实时更新块副本在簇上的位置信息
    * DataNode每隔3s发送一次heartbeat（包含众多信息，以方便NameNode调配）给NameNode，通知表明自己和副本均有效，若10min后NameNode未收到就认为该DataNode无效并且重新创建存储数据的DataNode
    * NameNode并不直接与DataNode取得联系，它使用对heartbeat的回复来对DataNode下达指令，指令包括
        * 把块复制到别的节点上
        * 移除本地块副本
        * 重新注册或者关闭节点
        * 马上发送一个块报告
* C. HDFS Client
    * 用户应用通过HDFS Client访问文件系统
    * 和大多数方便的文件系统类似，可以使用读、写、删除等操作目录或者文件
    * 用户在命名空间中通过路径获取文件和目录，不惜要了解系统元数据存储情况或者数据块有多个块副本
    * 提供访问文件数据块位置的API，允许应用修改文件的复制品数量
* D. Image and Journal
    * 为了防止checkpoint或者journal损毁，提出两个选择
        * 将checkpoint和journal存储在多个存储目录中，这防止了单卷故障造成的损失
        * 将目录放在不同的卷上，并将一个存储目录放在远程NFS服务器上，这防止了整个节点出现故障
    * NameNode是一个多线程操作系统，为了不让某一个事物的同步刷新操作影响事务保存到磁盘的整体过程，当一个线程启动同步刷新操作时，所有的事务将会被提交。其余线程只需要检查事务是否被保存，不需要启动刷新同步操作。
* E. CheckpointNode
    * HDFS中的NameNode除了处理客户端请求这个主要角色外还有两个其他的角色，检查点节点或者备份节点。角色在节点初始化时被确定。
    * 定期整合存在的检测点和日志来创建新的监测点和空日志，从NameNode上下载当前的检测点和日志，在本地合并，然后将新的检测点传回NameNode。
    * 经常在与NameNode不同的主机上面运行因为其与NameNode具有相同的内存要求
    * 定期穿件监测点可以保护文件系统元数据，当持久的命名空间复制块或者日志不可用时，系统可以从最近的检测点开始恢复
    * 创建监测点会让NameNode在新的检测点被上传的时候切断日志的尾部，以减少NameNode重启的时间和损毁的可能性。最好是创建一个每日的检测点
* F. BackupNode
    * 可以创建定期监测点节点，还可以维护一个石中玉NameNode同步的在内存中最新的文件系统命名空间映像
    * NameNode将BackupNode看作一个日志存储库，对待其像内存目录里的日志文件一样
    * 一旦NameNode损毁了，内存中的BackupNode映像和磁盘上的检测点就是最新的命名空间状态记录
    * 可以不用从活动状态的NameNode中下载检测点和日志文件就能创建一个新的检测点，因为在它自己内存中已经有最新版本的命名空间映像，这使得BackupNode更高效因为它只用把命名空间存到本地存储目录
    * 可被视作只读NameNode，除了块位置存储所有文件系统元数据信息，使用被分界点提供了在没有持久存储情况下运行NameNode的选项，将命名空间持久状态的责任委派给了备份节点
* G. Upgrades，File System Snapshots
    * 在系统升级期间，软件故障和人为错误引起的系统崩溃概率上升，创建快照的目的在于减小更新过程中系统存储数据损毁的潜在可能性
    * 快照能够存储当天前文件系统的状态，当更新导致数据丢失损毁时，可以将HDFS回滚到快照时的存储状态
    * 快照只能在系统开始时期创建，创建时，NameNode首先访问检测点和日志文件并将它们合并在内存中。之后在一个新的位置存储检测点和空的日志文件，这使得之前的旧检测点日志文件保持不变
    * 为了节省内存空间，每个DataNode创建存储目录的副本和存储文件块的硬链接
    * 系统更新只能往前回滚不能往后
    * 快照是所有集群工作而不是一个节点选择性事件
    * 需要coordinated snapshot来避免灾难性毁灭
* FILE I/O OPERATION AND REPLICA MANGEMENT
    * A. File Read and Write
        * HDFS文件由块组成，当数据包装满时就可以推送到pipeline中进行传送。数据写道HDFS文件中之后，HDFS不能保证所有写入的数据在文件关闭之前可读。用户可以直接调用hflush操作，那么当前数据就会被成功地传输，然后执行hflush操作。所有在执行hflush操作之前写入的数据都可读。
        * 收到成功建立连接的信号后，客户端就开始写入数据每次没有收到上一个数据包写入成功的信号之前就发送下一个数据包。但只要收到最后一个数据包成功传输的信号后才可以发送结束连接close信号，并收到成功关闭信号，传输才成功完成
        * hflush操作只能和数据包一起传递，不能成为一个单独的操作
        * HDFS读文件的时候，首次把存储在数据块中的校验和与根据接收数据计算出的校验和作匹配。如果匹配成功则接收数据否则提醒NameNode该数据损坏并且向另一个DataNode的块副本请求数据
        * 当客户端打开文件阅读时，首先获取块列表和块副本们的位置，并且将每一个块与他们的块副本排序，选择最近的获取数据阅读，如果失败了则尝试下一个最近的块。
        * HDFS 允许阅读可写的文件，但是需要先向其中一个块副本请求当前内容长度
    * B. Block Placement
        * 节点被放在不同的机架上，不同机架上的节点之间的带宽比相同机架上节点之间的带宽要大。
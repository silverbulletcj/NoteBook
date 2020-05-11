## 第九章 搭建Flink流式应用

* 部署模式
  * 独立集群
    * 一个Flink独立集群至少包含一个主进程和一个TaskManager进程
    * 主进程会启动单独的线程来运行Dispatch和ResourceManager
    * 为了在多台机器上搭建集群，需要调整默认配置
      * 将所有需要运行TaskManager的主机名(或IP地址)列在*./conf/slaves*文件中
        * Start-cluster.sh脚本需要针对所有运行TaskManager进程的机器配置无密码SSH登录
        * Flink发行版本目录在所有机器上的路径必须一致
        * 在*./conf/flink-conf.yaml*文件中将jobmanager.rpc.address一项配置为主进程所在机器的主机名
  * Docker
  * Apache Hadoop YARN
    * YARN是Apache Hadoop的资源管理组件，负责集群环境下的计算资源，家它们供给请求资源的应用
    * Flink能够以两种模式和YRN进行集成：作业模式和会话模式
      * 作业模式：Flink集群启动后只允许单个作业
      * 会话模式：系统会启动一个长时间运行的Flink集群，可以运行多个作业，需要手动停止
    * ResourceManager会自动对故障的TaskManager进行重启可以通过./conf/flink-conf.yaml配置文件控制Flink在YARN上的故障恢复行为
  * Kubernetes
    * Pod是Kubernetes是启动并管理的容器
    * 一个Deployment定义了一组需要运行的特定数量的Pod或容器
    * Kubernetes可能会在其集群的任意位置运行Pod
    * 在Kubernetes上搭建Flink需要两个Deployment来完成，一个用来运行主进程的Pod，另一个用于工作进程的Pod
* 高可用设置
  * Flink的HA设置需要运行一个Apache Zookeeper集群和一个持久化远程存储
  * 独立集群的HA设置
    * 在独立集群模式下，Flink需要后备Dispatch和TaskManager进程，用来接管故障进程的工作
    * 进行了HA设置，所有Dispatcher都会在ZooKeeper中注册，且会选出一个领导者，在故障时，会重新选择一个领导者，进行恢复故障应用
  * YARN上的HA设置
    * YARN会自动重启故障的ApplicationMaster，但会跟踪并限制重启次数以防出现无限的恢复循环
    * 需要在*./conf/flink-conf.yaml*中配置应用尝试重启的最大次数
  * Kubernetes上的HA设置
    * Kubernetes会自动重启故障容器以确保Pod运行数目的正确性
* 集成Hadoop组件
  * 使用针对特定版本Hadoop版本构建的Flink二进制发行版
  * 针对特定版本的Hadoop构建Flink
  * 使用不带Hadoop的Flink发行版并为Hadoop依赖手动配置Classpath
* 文件系统配置
  * Flink通过检查URI的协议来识别目标文件系统，常用的文件系统有：
    * 本地文件系统 file://
    * Hadoop HDFS hdfs://
    * Amazon S3 s3://
    * OpenStack Swift FS swift://
* 系统配置
  * Java和类加载
    * 为了防止作业和Flink使用相同依赖而引起冲突，Flink在默认情况下回先从用户类加载器中查找用户类，如果没有发现再从父类中加载，可以用classloader.resolve-order配置来翻转这一顺序
  * CPU
  * 内存和网络缓冲
  * 磁盘存储
  * 检查点和状态后端
  * 安全性
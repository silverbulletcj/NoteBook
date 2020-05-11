## 第十章 Flink和流式应用运维

* 运行并管理流式应用

  * 为了对主进程、工作进程以及应用进行监控，Flink对外公开的接口有

    * 一个用于提交可控制应用的命令行客户端工具
    * 一套用于命令行客户端和WEB UI的底层REST API
    * 一个用于提供有关Flink集群和当前运行应用详细信息及指标的Web UI

  * 保存点：需要由用户或者外部服务手动触发，且永远不会被Flink删除；每个保存点都对应一个持久化数据存储上的目录

  * 通过命令行客户端管理应用

    * 启动应用：*./bin/flink run ~/myApp.jar*,可以通过在命令行末尾附加参数的方式将其传递给应用的main()方法
    * 以分离模式提交应用：*./bin/flink run -d ~/myApp.jar*,不会等待应用结束，会立即返回并打印提交作业的JobID
    * 指定某个应用的默认并行度：*./bin/flink run -p 16 ~/myApp.jar*
    * 列出正在运行的应用：*./bin/flink list -r*
    * 生成和清除保存点：*./bin/flink savepoint <jobId> [savepointPath]*，*./bin/flink savepoint -d <savepoint>*
    * 取消应用：*./bin/flink cancel <jonId>*，在取消当前运行的应用之前生成一个保存点，在cancel命令后面加上-s参数，即*./bin/flink cacel -s [savepointPath] <jobId>*
    * 从保存点启动应用： *./bin/flink run -s <savepointPath> [options] <jobJar> [arguments]*

  * 通过REST API管理应用

    * 管理和监控Flink集群

      |               请求               | 参数                                         |                             响应                             |
      | :------------------------------: | -------------------------------------------- | :----------------------------------------------------------: |
      |          GET /overview           |                                              |                      展示基本的集群信息                      |
      |      GET /jobmanager/config      |                                              |       返回./conf/flink-conf/yaml中定义的JobManager配置       |
      |        GET  /taskmanagers        |                                              |     返回一个涵盖所有TaskManager的列表，包括ID和基本信息      |
      |     GET /jobmanager/metrics      |                                              |                 返回JobManager上可获得的指标                 |
      | GET /taskmanagers/<tmId>/metrics | tmId：连接的TaskManager的ID                  |              返回指定TaskManager上可获得的指标               |
      |         DELETE /cluster          |                                              |                     行为：关闭Flink集群                      |
      |        POST /jars/upload         | 文件以Multipart数据形式上传                  |                    上传jar文件的存储位置                     |
      |            GET /jars             |                                              |                   所有已上传JAR文件的列表                    |
      |       DELETE /jars/<jarId>       | jarId:使用列出JAR文件命令所得到的的JAR文件ID |                行为：删除给定ID对应的JAR文件                 |
      |      POST /jars/<jarId>/run      | jarId:启动应用的JAR文件ID                    |                      应用启动后的作业ID                      |
      |            GET  /jobs            |                                              | 所有正在运行应用的作业ID以及近期失败的、取消的和结束应用的作业ID列表 |
      |         GET /jbs/<jobId>         | jobId:使用列表应用命令得到的作业ID           |                         基本统计信息                         |
      |       PATCH /jobs/<jobId>        | jobId:使用列表应用命令得到的作业ID           |                        行为：取消应用                        |
      |  POST /jobs/<jobId>/savepoints   | jobId:使用列表应用命令得到的作业ID           |      一个用于检查保存点所触发的操作是否成功完成的请求ID      |
      |     POST /savepoint-disposal     | 需要以JSON对象的形式提供要删除的保存点路径   |            用于检查保存点是都已经成功删除的请求ID            |
      |  PATCH /jobs/<jobID>/rescaling   | jobId:使用列表应用命令得到的作业ID           |            一个用于检查扩缩容是否成功完成的请求ID            |

      

* 控制任务调度

  * 控制任务链
  * 定义处理槽共享组

* 调整检查点及恢复

  * 配置检查点
    * 可以通过StreamExecutionEnvironment启动
  * 启用检查点压缩
  * 应用停止后保留检查点
  * 配置状态后端
  * 配置故障恢复
    * 重启策略
      * Fixed-delay：以配置的固定间隔尝试将某个应用重启某个固定次数
      * Failure-rate：允许在未超过故障率的前提下不断重启应用
      * No-restart：不会重启应用，让它直接失败
    * 本地恢复
      * State.backend.local-recovery：用于启用或禁用本地恢复；默认禁用
      * Taskmanager.state.local.root-dirs：用于指定用于存储本地状态副本的路径

* 监控Flink集群和应用

  * Flink Web UI
  * 指标系统

* 配置日志行为
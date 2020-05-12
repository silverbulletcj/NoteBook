## QueryableState

* 官方文档介绍

  * 用于可查询式状态的客户端API当前处于发展中，无法保证接口的稳定性

  * QueryableState能够将所有**键值分区状态**暴露给外部系统

  * 要使用可查询式状态，需要三个实体

    * QueryableStateClient，可能在集群外部运行且提交用户的请求
    * QueryableStateClientProxy，在每个TaskManager上运行，负责接收客户端的请求，并将响应返还给客户端
    * QueryableStateServer，在每个TaskManager上运行，并负责提供本地状态存储

  * 客户端连接到代理之一，然后发送一个带有特定键值K的请求，为了能够查询该键对应的状态在哪个TaskManager上，代理将会从JobManager上查询，得到回答之后，就会从正在运行的TaskManager上查询正在运行的对应K的状态，将其返回给客户端

  * 为了使用Queryable State，需要做如下配置

    * 需要将*flink-queryable-state-runtime_2.11-1.9.2.jar*导入TaskManager进程的classpath
      * 将*queryable-state.enable*设置为true，该内容的配置在*./conf/flink-conf.yaml*下

  * 为了让State Queryable，可以通过两种配置实现

    * QueryableStateStream，一个能够充当Sink的对象，能够将它的输入当做Queryable State

      * 在一个KeyedStream上调用Calling.asQueryableState(stateName, stateDescriptor)会返回一个QueryableStateStream，它提供值作为可查询状态，根据状态的类型，可以有以下变种：

        ```java
        // ValueState
        QueryableStateStream asQueryableState(
            String queryableStateName,
            ValueStateDescriptor stateDescriptor)
        
        // Shortcut for explicit ValueStateDescriptor variant
        QueryableStateStream asQueryableState(String queryableStateName)
        
        // FoldingState
        QueryableStateStream asQueryableState(
            String queryableStateName,
            FoldingStateDescriptor stateDescriptor)
        
        // ReducingState
        QueryableStateStream asQueryableState(
            String queryableStateName,
            ReducingStateDescriptor stateDescriptor)
        ```

      * 注意并没有可查询的*ListState*，因为它可能会导致无限增长的list，从而导致内存耗尽

        * 返回的QueryableStateStream可以被看作无法再进行转换的sink；在内部，QueryableStateStream被转换成一个用所有输入值来更新可查询状态实例的运算符，更新逻辑是由asQueryableState调用中StateDescription隐含

    * StateDescriptor.setQueryable(queryableStateName)方法，可以让状态描述符对应的状态的值可查询

      * 对于键值分区状态，都可以使用StateDescriptor.setQueryable(String queryableStateName)方法使得状态暴露给外部应用

  * 要从外部系统中查询状态

    * 添加依赖到项目中

      ```java
      <dependency>
        <groupId>org.apache.flink</groupId>
        <artifactId>flink-core</artifactId>
        <version>1.9.2</version>
      </dependency>
      <dependency>
        <groupId>org.apache.flink</groupId>
        <artifactId>flink-queryable-state-client-java</artifactId>
        <version>1.9.2</version>
      </dependency>
      ```

    * 初始化客户端需要配置有效的TaskManager的hostname和代理端口

    * 为了能够查询状态，要使用如下方法：

      ```java
      CompletableFuture<S> getKvState(
      	JobID jobId,  //运行应用的JobID
        String queryableStateName,  //可查询状态标识符
        K key,  //所需状态的键值
        TypeInfromation<K> keyTypeInfo, //键值的类型信息
        StateDescriptor<S, V> stateDescriptor) //可查询状态的StateDescriptor
      )
      ```

    * 返还的Future对象包含类型S，即查询的状态的类型：`ValueState`, `ReduceState`, `ListState`, `MapState`, `AggregatingState`, FoldingState.

    * 这些返回的状态只能进行访问，不能进行修改，否则会报UnsupportedOperationException的错

  
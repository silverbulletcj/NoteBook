## 第四章 设置Apache Flink开发环境

* 创建Flink Maven项目

  * 命令

    ```java
    mvn archetype:generate \
      -DarchetypeGroupId=org.apache.flink \
      -DarchetypeArtifactId=flink-quickstart-java \
      -DarchetypeVersion=1.9.2 \
      -DgroupId=org.apache.flink.quickstart \
      -DartifactId=flink-java-project \
      -Dversion=0.1 \
      -Dpackage=org.apache.flink.quickstart \
    ```

  * 这样能够在flink-java-project文件夹内生成一个Flink1.9.2版本的Maven项目

  * 可以将项目导入IDE中，也可以通过mvn clean package -Pbulid-jar得到一个jar文件
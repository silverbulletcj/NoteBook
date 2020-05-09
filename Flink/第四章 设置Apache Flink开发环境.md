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

* Flink核心依赖

  ```java
  <dependency>
    <groupId>org.apache.flink</groupId>
    <artifactId>flink-java</artifactId>
    <version>1.9.0</version>
    <scope>provided</scope>
  </dependency>
  <dependency>
    <groupId>org.apache.flink</groupId>
    <artifactId>flink-streaming-java_2.11</artifactId>
    <version>1.9.0</version>
    <scope>provided</scope>
  </dependency>
  ```

  * 这些核心依赖需要有provided的scope，这意味着这些依赖会编译，但是不会打包进最终的jar文件中；如果将provided去除，可能会导致得到的JAR会特别的大

  * 为了能够在Intellij上运行文件，需要添加如下的依赖

    ```java
    <!-- This profile helps to make things run out of the box in IntelliJ -->
        <!-- Its adds Flink's core classes to the runtime class path. -->
        <!-- Otherwise they are missing in IntelliJ, because the dependency is 'provided' -->
        <profiles>
            <profile>
                <id>add-dependencies-for-IDEA</id>
    
                <activation>
                    <property>
                        <name>idea.version</name>
                    </property>
                </activation>
    
                <dependencies>
                    <dependency>
                        <groupId>org.apache.flink</groupId>
                        <artifactId>flink-java</artifactId>
                        <version>${flink.version}</version>
                        <scope>compile</scope>
                    </dependency>
                    <dependency>
                        <groupId>org.apache.flink</groupId>
                        <artifactId>flink-streaming-java_${scala.binary.version}</artifactId>
                        <version>${flink.version}</version>
                        <scope>compile</scope>
                    </dependency>
                </dependencies>
            </profile>
        </profiles>
    ```

    
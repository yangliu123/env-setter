#Flume开发环境搭建
单个节点部署如下：
官方网站下载程序包
http://flume.apache.org/download.html
版本 1.5.2

解压
$ tar -zxvf apache-flume-1.5.2-SNAPSHOT-bin.tar.gz
$ cd apache-flume-1.5.2-SNAPSHOT-bin

配置环境文件
$ sudo cp conf/flume-env.sh.template conf/flume-env.sh

修改配置文件
$ sudo vi conf/flume-conf.properties
参考示例文件：在示例文件中使用到了自定义的jar包，所以还需要把自定义jar包放到flume的classpath下

编译打包自定义jar包
$ git clone git@git.unionpaysmart.com:upsmart/upsmart-flume-hbase-sink.git
$ cd upsmart-flume-hbase-sink
$ mvn clean install
$ cp target/upsmart-flume-hbase-sink-0.0.1-SNAPSHOT.jar $FLUME_HOME/lib/

启动flume
bin/flume-ng agent -n <agent-name> -c conf/ -f conf/flume-conf.properties -Dflume.root.logger=DEBUG,console

Edit
在项目中配置log4j
项目pom.xml文件中加入依赖
<dependency>
<groupId>org.apache.flume.flume-ng-clients</groupId>
<artifactId>flume-ng-log4jappender</artifactId>
<version>1.5.2</version>
</dependency>

配置log4j.xml
<!-- 单节点flume输出 -->
<appender name="flume-avro" 
class="org.apache.flume.clients.log4jappender.Log4jAppender">
<param name="Hostname" value="arreat00" />
<param name="Port" value="4441" />
<param name="UnsafeMode" value="true" />
<layout class="org.apache.log4j.PatternLayout">
<param name="ConversionPattern" value="%d{yyyy-MM-dd HH:mm:ss} [%l] [%rms] %m%n" />
</layout>
</appender>

<!-- 多节点flume输出 -->
<appender name="flume-load-balancing-avro" 
class="org.apache.flume.clients.log4jappender.LoadBalancingLog4jAppender">
<param name="Hosts" value="arreat00:4441 arreat00:4442" />
<param name="Selector" value="ROUND_ROBIN" />
<param name="MaxBackoff" value="2000" />
<layout class="org.apache.log4j.PatternLayout">
<param name="ConversionPattern" value="%d{yyyy-MM-dd HH:mm:ss} [%l] [%rms] %m%n" />
</layout>
</appender>

<!-- 异步日志输出 -->
<appender name="flume-async" class="org.apache.log4j.AsyncAppender">
<param name="BufferSize" value="256" />
<appender-ref ref="flume-load-balancing-avro" />
</appender>

Edit
推荐设置
在不同的服务器上启动3个flume
在系统中配置log4j时使用LoadBalancingLog4jAppender



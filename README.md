# Introduction
Apache Spark uses Hive 0.13 internally. If you are using Hive 0.14 (Hadoop 2.5 and up) this normally functions well, 
but if you are running a secured cluster, ie. kerberized, it stops functioning. This is due to a change in protocol and also in some further requests. Some changes where made in Spark 1.4 that allow for a separated hive to be loaded. Unfortunately, if running on Yarn a connection is required earlier in the process and it still fails.

To fix this some of the changes from Hive 0.14 need to be backported. Spark allows to add extra jars to the classpath (spark-env.sh) so this is great, however it seems that some of the classes are being picked up earlier than the one added. This means that we need to at least rip one of the class files out out the spark-assembly jar.

Some scripts will be supplied to automate this for you and to either include the newly generated classes inside the assembly or to have it in a separate jar that you can load through the spark-env.sh settings. 

# Installation
After cloning the source, make sure you edit the pom.xml to reflect your hadoop version (standard 2.6). Then build it by issuing

> mvn package

Copy the created jar to a shared location ${SPARK_HOME}/lib would be a good start.

Now edit conf/spark-env.sh to contain the location to the jar, ie. SPARK_DIST_CLASSPATH=<<<SPARK_HOME>>>/lib/spark-hive014-compat-0.13.1a.jar . If you are using the bundled Hadoop also add guava.jar to your class path. It is normally found where the Hadoop client librariers are (execute hadoop classpath to find out).

Two classes need to be remove from the spark-assembly jar. 

> zip -d spark-assembly-XX.XX.jar org/apache/hadoop/hive/ql/security/authorization/plugin/sqlstd/SQLStdHiveAccessController*
> zip -d spark-assembly-XX.XX.jar org/apache/hadoop/hive/thrift/HadoopThriftAuthBridge20S*

And you should be good to go :-).

# Introduction
Apache Spark uses Hive 0.13 internally. If you are using Hive 0.14 (Hadoop 2.5 and up) this normally functions well, 
but if you are running a secured cluster, ie. kerberized, it stops functioning. This is due to a change in protocol and also in some further requests. Some changes where made in Spark 1.4 that allow for a separated hive to be loaded. Unfortunately, if running on Yarn a connection is required earlier in the process and it still fails.

To fix this some of the changes from Hive 0.14 need to be backported. Spark allows to add extra jars to the classpath (spark-env.sh) so this is great, however it seems that some of the classes are being picked up earlier than the one added. This means that we need to at least rip one of the class files out out the spark-assembly jar.

Some script will be supplied to automate this for you and to either include the newly generated classes inside the assembly or to have it in a separate jar that you can load through the spark-env.sh settings. 

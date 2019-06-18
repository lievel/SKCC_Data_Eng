# 시험대비 정리

## Sqoop

Import
<pre>
sqoop import --table accounts --connect jdbc:mysql://localhost/loudacre --username training --password training --fields-terminated-by "\t" --target-dir /test-sqoop

sqoop import \
   --connect jdbc:mysql://mysql.example.com/mydb \
   --username sqoop \
   --password sqoop \
   --table acclog \
   --fields-terminated-by '\t' \
   --target-dir /teststore/input/acclog

sqoop import-all-tables \
   --connect jdbc:mysql://mysql.example.com/mydb \
   --username sqoop \
   --password sqoop \
   --exclude-tables cities,countries \
   --warehouse-dir /teststore/input/
</pre>

Export
<pre>
sqoop export \
   --connect jdbc:mysql://mysql.example.com/mydb \
   --username sqoop \
   --password sqoop \
   --table acclog \
   --input-fields-terminated-by '\t'
   --export-dir /teststore/input/acclog
</pre>


## Hive 준비 사항
https://www.cloudera.com/documentation/enterprise/5-15-x/topics/cdh_ig_hive_metastore_configure.html
<pre>
$ sudo yum install mysql-connector-java
$ ln -s /usr/share/java/mysql-connector-java.jar /usr/lib/hive/lib/mysql-connector-java.jar
</pre>

<pre>
Create external table A (
column1 string,
column2 string)
ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t'
LOCATION '/data/tableA';

</pre>

## Flume
<pre>
cat flume-examples.conf 
# Define sources, sinks, and channel for agent named 'agent1'
agent1.sources = src1
agent1.sinks = sink1
agent1.channels = ch1

agent1.channels.ch1.type = memory

# Configure the source
agent1.sources.src1.type = spooldir
agent1.sources.src1.spoolDir = /var/flume/incoming
agent1.sources.src1.channel = ch1

# Configure the sink
agent1.sinks.sink1.type = hdfs
agent1.sinks.sink1.hdfs.path = /loudacre/logdata/%y-%m-%d
agent1.sinks.sink1.hdfs.fileType = DataStream
agent1.sinks.sink1.hdfs.fileSuffix = .txt
agent1.sinks.sink1.hdfs.codeC = snappy
agent1.sinks.sink1.channel = ch1
</pre>>







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
</pre>

<pre>
flume-ng agent \
> --conf /etc/flume-ng/conf \
> --conf-file $DEVSH/exercises/flume/new_flume_config.conf \
> --name agent1 -Dflume.root.logger=INFO,console
</pre>


## Spark
<pre>
# Stub code to copy into Spark Shell

import xml.etree.ElementTree as ElementTree

# Optional: Set logging level to WARN to reduce distracting info messages
sc.setLogLevel("WARN")  

# Given a string containing XML, parse the string, and 
# return an iterator of activation XML records (Elements) contained in the string

def getActivations(s):
    filetree = ElementTree.fromstring(s)
    return filetree.getiterator('activation')
    
# Given an activation record (XML Element), return the model name
def getModel(activation):
    return activation.find('model').text 

# Given an activation record (XML Element), return the account number 
def getAccount(activation):
    return activation.find('account-number').text 

# Exercise solution
# Read XML files into an RDD 
files="/loudacre/activations"
activationFiles = sc.wholeTextFiles(files)

# Parse each file (as a string) into a collection of activation XML records
activationRecords = activationFiles.flatMap(lambda (filename,xmlstring): getActivations(xmlstring))

# Map each activation record to "account-number:model-name"
models = activationRecords.map(lambda record: getAccount(record) + ":" + getModel(record))

# Save the data to a file
models.saveAsTextFile("/loudacre/account-models")

</pre>

<pre>
# Create an RDD based on a data file
myrdd = sc.textFile("file:/home/training/training_materials/data/frostroad.txt")

# Count the number of lines in the RDD
myrdd.count()

# Display all the lines in the RDD
myrdd.collect()

# Optional: Set logging level to WARN to reduce distracting info messages
sc.setLogLevel("WARN")  

# Create an RDD based on the web log data files
logfiles="/loudacre/weblogs/*"
logsRDD = sc.textFile(logfiles)

# Count the number of records (lines) in the RDD
logsRDD.count()

# Display the first 10 lines which are requests for JPG files
jpglogsRDD=logsRDD.filter(lambda line: ".jpg" in line)
jpglogsRDD.take(10)

# Display the JPG requests, this time using a single command line
sc.textFile(logfiles).filter(lambda line: ".jpg" in line).count()

# Create an RDD of the length of each line in the file and display the first 5 line lengths
logsRDD.map(lambda line: len(line)).take(5)

# Map the log data to an RDD of arrays of the words on each line
logsRDD.map(lambda line: line.split(' ')).take(5)

# Map the log data to an RDD of IP addresses for each line 
ipsRDD = logsRDD.map(lambda line: line.split(' ')[0])
ipsRDD.take(5)

# Loop through the array returned by take
for ip in ipsRDD.take(10): print ip

# Save the IP addresses to text file(s)
ipsRDD.saveAsTextFile("/loudacre/iplist")

# Bonus Exercise - Display "ip-address/user-id" for the first 5 HTML requests 
# in the data set 
htmllogsRDD=logsRDD.filter(lambda line: ".htm" in line).map(lambda line: (line.split()[0],line.split()[2]))
for x in htmllogsRDD.take(5): print x[0]+"/"+x[1]

</pre>







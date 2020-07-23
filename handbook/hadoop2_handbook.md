# General

## [Overview](https://hadoop.apache.org/docs/stable2/)

## [Single Node Setup](https://hadoop.apache.org/docs/stable2/hadoop-project-dist/hadoop-common/SingleCluster.html)

### [Prerequisites](https://hadoop.apache.org/docs/stable2/hadoop-project-dist/hadoop-common/SingleCluster.html#Prerequisites)

#### [Required Software](https://hadoop.apache.org/docs/stable2/hadoop-project-dist/hadoop-common/SingleCluster.html#Required_Software)

```
sudo apt-get install openjdk-8-jdk
```

Set JAVA_HOME

```
ls -l /usr/lib/jvm/java-8-openjdk-amd64/
export JAVA_HOME=/usr/lib/jvm/java-8-openjdk-amd64
echo $JAVA_HOME
```

### [Download](https://hadoop.apache.org/docs/stable2/hadoop-project-dist/hadoop-common/SingleCluster.html#Download)

```
tar -zxvf hadoop-2.10.0.tar.gz -C ~/soft/hadoop/
```

### [Prepare to Start the Hadoop Cluster](https://hadoop.apache.org/docs/stable2/hadoop-project-dist/hadoop-common/SingleCluster.html#Prepare_to_Start_the_Hadoop_Cluster)

```
bin/hadoop
```

### [Standalone Operation](https://hadoop.apache.org/docs/stable2/hadoop-project-dist/hadoop-common/SingleCluster.html#Standalone_Operation)

```
mkdir input
cp etc/hadoop/*.xml input
bin/hadoop jar share/hadoop/mapreduce/hadoop-mapreduce-examples-2.10.0.jar grep input output 'dfs[a-z.]+'
cat output/*
```

### [Pseudo-Distributed Operation](https://hadoop.apache.org/docs/stable2/hadoop-project-dist/hadoop-common/SingleCluster.html#Pseudo-Distributed_Operation)

#### [Configuration](https://hadoop.apache.org/docs/stable2/hadoop-project-dist/hadoop-common/SingleCluster.html#Configuration)

```
vi etc/hadoop/core-site.xml
```

```
<configuration>
    <property>
        <name>fs.defaultFS</name>
        <value>hdfs://localhost:9000</value>
    </property>
</configuration>
```

```
vi etc/hadoop/hdfs-site.xml
```

```
<configuration>
    <property>
        <name>dfs.replication</name>
        <value>1</value>
    </property>
</configuration>
```

#### [Setup passphraseless ssh](https://hadoop.apache.org/docs/stable2/hadoop-project-dist/hadoop-common/SingleCluster.html#Setup_passphraseless_ssh)

```
ssh-keygen -t rsa -P '' -f ~/.ssh/id_rsa
cat ~/.ssh/id_rsa.pub >> ~/.ssh/authorized_keys
ls -la ~/.ssh/
chmod 0600 ~/.ssh/authorized_keys
ssh localhost
```

#### [Execution](https://hadoop.apache.org/docs/stable2/hadoop-project-dist/hadoop-common/SingleCluster.html#Execution)

```
vi etc/hadoop/hadoop-env.sh
```

```
#export JAVA_HOME=${JAVA_HOME}
export JAVA_HOME=/usr/lib/jvm/java-8-openjdk-amd64
```

```
bin/hdfs namenode -format
sbin/start-dfs.sh
```

Browse the web interface for the NameNode; by default it is available at:

```
http://localhost:50070/
http://10.20.20.1:50070/
```

Make the HDFS directories required to execute MapReduce jobs:

```
bin/hdfs dfs -mkdir /user
bin/hdfs dfs -mkdir /user/alvin
bin/hdfs dfs -ls /
```

Copy the input files into the distributed filesystem:

```
bin/hdfs dfs -put etc/hadoop /input
bin/hdfs dfs -ls /
```

Run some of the examples provided:

```
bin/hadoop jar share/hadoop/mapreduce/hadoop-mapreduce-examples-2.10.0.jar grep input output 'dfs[a-z.]+'
```

Examine the output files: Copy the output files from the distributed filesystem to the local filesystem and examine them:

```
bin/hdfs dfs -get input input11
ls -l input11
bin/hdfs dfs -ls /input
```

When you’re done, stop the daemons with:

```
sbin/stop-dfs.sh
```

#### [YARN on a Single Node](https://hadoop.apache.org/docs/stable2/hadoop-project-dist/hadoop-common/SingleCluster.html#YARN_on_a_Single_Node)

Configure parameters as follows:

```
vi etc/hadoop/mapred-site.xml
```

```
<configuration>
    <property>
        <name>mapreduce.framework.name</name>
        <value>yarn</value>
    </property>
</configuration>
```

```
vi etc/hadoop/yarn-site.xml
```

```
<configuration>
    <property>
        <name>yarn.nodemanager.aux-services</name>
        <value>mapreduce_shuffle</value>
    </property>
</configuration>
```

Start ResourceManager daemon and NodeManager daemon:

```
sbin/start-yarn.sh
```

Browse the web interface for the ResourceManager; by default it is available at:

```
http://localhost:8088/
http://10.20.20.1:8088/
```

Run a MapReduce job.

When you’re done, stop the daemons with:

```
sbin/stop-yarn.sh
```

### [Fully-Distributed Operation](https://hadoop.apache.org/docs/stable2/hadoop-project-dist/hadoop-common/SingleCluster.html#Fully-Distributed_Operation)

## [FileSystem Shell](https://hadoop.apache.org/docs/stable2/hadoop-project-dist/hadoop-common/FileSystemShell.html)

### [cat](https://hadoop.apache.org/docs/stable2/hadoop-project-dist/hadoop-common/FileSystemShell.html#cat)

```
hadoop fs -cat hdfs://nn1.example.com/file1 hdfs://nn2.example.com/file2
hadoop fs -cat file:///file3 /user/hadoop/file4
```

### [chmod](https://hadoop.apache.org/docs/stable2/hadoop-project-dist/hadoop-common/FileSystemShell.html#chmod)

```
hadoop fs -chmod [-R] <MODE[,MODE]... | OCTALMODE> URI [URI ...]
```

### [chown](https://hadoop.apache.org/docs/stable2/hadoop-project-dist/hadoop-common/FileSystemShell.html#chown)

```
hadoop fs -chown [-R] [OWNER][:[GROUP]] URI [URI ]
```

### [count](https://hadoop.apache.org/docs/stable2/hadoop-project-dist/hadoop-common/FileSystemShell.html#count)

```
hadoop fs -count hdfs://nn1.example.com/file1 hdfs://nn2.example.com/file2
hadoop fs -count -q hdfs://nn1.example.com/file1
hadoop fs -count -q -h hdfs://nn1.example.com/file1
hadoop fs -count -q -h -v hdfs://nn1.example.com/file1
hadoop fs -count -u hdfs://nn1.example.com/file1
hadoop fs -count -u -h hdfs://nn1.example.com/file1
hadoop fs -count -u -h -v hdfs://nn1.example.com/file1
```

### [cp](https://hadoop.apache.org/docs/stable2/hadoop-project-dist/hadoop-common/FileSystemShell.html#cp)

```
hadoop fs -cp /user/hadoop/file1 /user/hadoop/file2
hadoop fs -cp /user/hadoop/file1 /user/hadoop/file2 /user/hadoop/dir
```

### [df](https://hadoop.apache.org/docs/stable2/hadoop-project-dist/hadoop-common/FileSystemShell.html#df)

```
hadoop dfs -df /user/hadoop/dir1
```

### [du](https://hadoop.apache.org/docs/stable2/hadoop-project-dist/hadoop-common/FileSystemShell.html#du)

```
hadoop fs -du /user/hadoop/dir1 /user/hadoop/file1 hdfs://nn.example.com/user/hadoop/dir1
```

### [find](https://hadoop.apache.org/docs/stable2/hadoop-project-dist/hadoop-common/FileSystemShell.html#find)

```
hadoop fs -find / -name test -print
```

### [get](https://hadoop.apache.org/docs/stable2/hadoop-project-dist/hadoop-common/FileSystemShell.html#get)

```
hadoop fs -get /user/hadoop/file localfile
hadoop fs -get hdfs://nn.example.com/user/hadoop/file localfile
```

### [ls](https://hadoop.apache.org/docs/stable2/hadoop-project-dist/hadoop-common/FileSystemShell.html#ls)

```
hadoop fs -ls /user/hadoop/file1
```

### [mkdir](https://hadoop.apache.org/docs/stable2/hadoop-project-dist/hadoop-common/FileSystemShell.html#mkdir)

```
hadoop fs -mkdir /user/hadoop/dir1 /user/hadoop/dir2
hadoop fs -mkdir hdfs://nn1.example.com/user/hadoop/dir hdfs://nn2.example.com/user/hadoop/dir
```

### [mv](https://hadoop.apache.org/docs/stable2/hadoop-project-dist/hadoop-common/FileSystemShell.html#mv)

```
hadoop fs -mv /user/hadoop/file1 /user/hadoop/file2
hadoop fs -mv hdfs://nn.example.com/file1 hdfs://nn.example.com/file2 hdfs://nn.example.com/file3 hdfs://nn.example.com/dir1
```

### [put](https://hadoop.apache.org/docs/stable2/hadoop-project-dist/hadoop-common/FileSystemShell.html#put)

```
hadoop fs -put localfile /user/hadoop/hadoopfile
hadoop fs -put -f localfile1 localfile2 /user/hadoop/hadoopdir
hadoop fs -put -d localfile hdfs://nn.example.com/hadoop/hadoopfile
hadoop fs -put - hdfs://nn.example.com/hadoop/hadoopfile Reads the input from stdin.
```

### [rm](https://hadoop.apache.org/docs/stable2/hadoop-project-dist/hadoop-common/FileSystemShell.html#rm)

```
hadoop fs -rm hdfs://nn.example.com/file /user/hadoop/emptydir
```

### [rmdir](https://hadoop.apache.org/docs/stable2/hadoop-project-dist/hadoop-common/FileSystemShell.html#rmdir)

```
hadoop fs -rmdir /user/hadoop/emptydir
```

### [tail](https://hadoop.apache.org/docs/stable2/hadoop-project-dist/hadoop-common/FileSystemShell.html#tail)

```
hadoop fs -tail pathname
```

# [HDFS](https://hadoop.apache.org/docs/stable2/hadoop-project-dist/hadoop-hdfs/HDFSCommands.html)

## [User Commands](https://hadoop.apache.org/docs/stable2/hadoop-project-dist/hadoop-hdfs/HDFSCommands.html#User_Commands)

### [dfs](https://hadoop.apache.org/docs/stable2/hadoop-project-dist/hadoop-hdfs/HDFSCommands.html#dfs)

Usage: `hdfs dfs [COMMAND [COMMAND_OPTIONS]]`

Run a filesystem command on the file system supported in Hadoop. The various COMMAND_OPTIONS can be found at [File System Shell Guide](https://hadoop.apache.org/docs/stable2/hadoop-project-dist/hadoop-common/FileSystemShell.html).

```
hdfs dfs -ls /
hadoop fs -ls /
```


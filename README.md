# Accumulo-Pig for Pig 0.11 and Accumulo 1.6.0

This project is a slight modification of the work done in https://github.com/jt6211/accumulo-pig to update it to use Accumulo 1.6.0 with Pig 0.11 as found in within Cloudera.

This has been tested against the Cloudera Quickstart VM for CDH 4.7.0.

## Setup

To compile the project run:

    mvn package

You will then need to download the JAR files which will be needed by pig:

    mvn dependency:copy-dependencies -DoutputDirectory=lib  -DincludeArtifactIds=zookeeper,libthrift,accumulo-core,accumulo-fate,accumulo-trace

This will add the JAR files to the 'lib' directory in the project folder. Transfer these JAR files along with the compiled JAR file 'accumulo-pig-1.6.0.jar' found in the target/ directory to the target machine. These should be local on each node.
I transfer them into the /lib/ directory on the node.

To write a pig script that uses Accumulo you should register the JAR files with pig. To do this run the commands:

    register /lib/accumulo-core-1.6.0.jar;
    register /lib/accumulo-fate-1.6.0.jar;
    register /lib/accumulo-pig-1.6.0.jar;
    register /lib/accumulo-trace-1.6.0.jar;
    register /lib/libthrift-0.9.0.jar;
    register /lib/zookeeper-3.3.6.jar;

Save the register commands to a file such as "register-accumulo-jars.pig", it is then possible to run "exec register-accumulo-jars.pig" in pig's grunt interface. Alternatively place them at the top of any Pig script you create.

## Usage

Use the below commands to interact with accumulo

To Load (fill in the %XYZ%'s with the correct parameter):

    DATA = LOAD 'accumulo://%TableName%?instance=%instanceName%&user=%AccumuloUser%&pass=%AccumuloPassword%&zookeepers=%ZookeeperServers%' using org.apache.accumulo.pig.AccumuloStorage() AS (row, cf, cq, cv, ts, val);

To Store:

    STORE DATA into 'accumulo://%TableName%?instance=%instanceName%&user=%AccumuloUser%&pass=%AccumuloPassword%&zookeepers=%ZookeeperServers%' using org.apache.accumulo.pig.AccumuloStorage();

## Example

    [cloudera@localhost ~]$ pig
    2015-04-15 13:44:43,920 [main] INFO  org.apache.pig.Main - Apache Pig version 0.11.0-cdh4.7.0 (rexported) compiled May 28 2014, 11:05:48
    2015-04-15 13:44:43,920 [main] INFO  org.apache.pig.Main - Logging error messages to: /home/cloudera/pig_1429130683916.log
    2015-04-15 13:44:43,945 [main] INFO  org.apache.pig.impl.util.Utils - Default bootup file /root/.pigbootup not found
    2015-04-15 13:44:44,192 [main] WARN  org.apache.hadoop.conf.Configuration - fs.default.name is deprecated. Instead, use fs.defaultFS
    2015-04-15 13:44:44,193 [main] INFO  org.apache.pig.backend.hadoop.executionengine.HExecutionEngine - Connecting to hadoop file system at: hdfs://localhost.localdomain:8020
    2015-04-15 13:44:44,823 [main] INFO  org.apache.pig.backend.hadoop.executionengine.HExecutionEngine - Connecting to map-reduce job tracker at: localhost.localdomain:8021
    2015-04-15 13:44:44,825 [main] WARN  org.apache.hadoop.conf.Configuration - fs.default.name is deprecated. Instead, use fs.defaultFS
    grunt> exec register-accumulo-jars.pig
    2015-04-15 13:45:17,942 [main] WARN  org.apache.hadoop.conf.Configuration - dfs.df.interval is deprecated. Instead, use fs.df.interval
    2015-04-15 13:45:17,942 [main] WARN  org.apache.hadoop.conf.Configuration - dfs.max.objects is deprecated. Instead, use dfs.namenode.max.objects
    2015-04-15 13:45:17,942 [main] WARN  org.apache.hadoop.conf.Configuration - hadoop.native.lib is deprecated. Instead, use io.native.lib.available
    2015-04-15 13:45:17,942 [main] WARN  org.apache.hadoop.conf.Configuration - dfs.data.dir is deprecated. Instead, use dfs.datanode.data.dir
    2015-04-15 13:45:17,943 [main] WARN  org.apache.hadoop.conf.Configuration - dfs.name.dir is deprecated. Instead, use dfs.namenode.name.dir
    2015-04-15 13:45:17,944 [main] WARN  org.apache.hadoop.conf.Configuration - fs.default.name is deprecated. Instead, use fs.defaultFS
    2015-04-15 13:45:17,944 [main] WARN  org.apache.hadoop.conf.Configuration - fs.checkpoint.dir is deprecated. Instead, use dfs.namenode.checkpoint.dir
    2015-04-15 13:45:17,944 [main] WARN  org.apache.hadoop.conf.Configuration - dfs.block.size is deprecated. Instead, use dfs.blocksize
    2015-04-15 13:45:17,944 [main] WARN  org.apache.hadoop.conf.Configuration - dfs.access.time.precision is deprecated. Instead, use dfs.namenode.accesstime.precision
    2015-04-15 13:45:17,944 [main] WARN  org.apache.hadoop.conf.Configuration - dfs.replication.min is deprecated. Instead, use dfs.namenode.replication.min
    2015-04-15 13:45:17,944 [main] WARN  org.apache.hadoop.conf.Configuration - dfs.name.edits.dir is deprecated. Instead, use dfs.namenode.edits.dir
    2015-04-15 13:45:17,944 [main] WARN  org.apache.hadoop.conf.Configuration - dfs.replication.considerLoad is deprecated. Instead, use dfs.namenode.replication.considerLoad
    2015-04-15 13:45:17,945 [main] WARN  org.apache.hadoop.conf.Configuration - dfs.balance.bandwidthPerSec is deprecated. Instead, use dfs.datanode.balance.bandwidthPerSec
    2015-04-15 13:45:17,945 [main] WARN  org.apache.hadoop.conf.Configuration - dfs.safemode.threshold.pct is deprecated. Instead, use dfs.namenode.safemode.threshold-pct
    2015-04-15 13:45:17,945 [main] WARN  org.apache.hadoop.conf.Configuration - dfs.http.address is deprecated. Instead, use dfs.namenode.http-address
    2015-04-15 13:45:17,945 [main] WARN  org.apache.hadoop.conf.Configuration - dfs.name.dir.restore is deprecated. Instead, use dfs.namenode.name.dir.restore
    2015-04-15 13:45:17,945 [main] WARN  org.apache.hadoop.conf.Configuration - dfs.https.client.keystore.resource is deprecated. Instead, use dfs.client.https.keystore.resource
    2015-04-15 13:45:17,945 [main] WARN  org.apache.hadoop.conf.Configuration - dfs.backup.address is deprecated. Instead, use dfs.namenode.backup.address
    2015-04-15 13:45:17,945 [main] WARN  org.apache.hadoop.conf.Configuration - dfs.backup.http.address is deprecated. Instead, use dfs.namenode.backup.http-address
    2015-04-15 13:45:17,945 [main] WARN  org.apache.hadoop.conf.Configuration - dfs.permissions is deprecated. Instead, use dfs.permissions.enabled
    2015-04-15 13:45:17,945 [main] WARN  org.apache.hadoop.conf.Configuration - dfs.safemode.extension is deprecated. Instead, use dfs.namenode.safemode.extension
    2015-04-15 13:45:17,946 [main] WARN  org.apache.hadoop.conf.Configuration - dfs.datanode.max.xcievers is deprecated. Instead, use dfs.datanode.max.transfer.threads
    2015-04-15 13:45:17,946 [main] WARN  org.apache.hadoop.conf.Configuration - dfs.https.need.client.auth is deprecated. Instead, use dfs.client.https.need-auth
    2015-04-15 13:45:17,946 [main] WARN  org.apache.hadoop.conf.Configuration - dfs.https.address is deprecated. Instead, use dfs.namenode.https-address
    2015-04-15 13:45:17,946 [main] WARN  org.apache.hadoop.conf.Configuration - dfs.replication.interval is deprecated. Instead, use dfs.namenode.replication.interval
    2015-04-15 13:45:17,946 [main] WARN  org.apache.hadoop.conf.Configuration - fs.checkpoint.edits.dir is deprecated. Instead, use dfs.namenode.checkpoint.edits.dir
    2015-04-15 13:45:17,946 [main] WARN  org.apache.hadoop.conf.Configuration - dfs.write.packet.size is deprecated. Instead, use dfs.client-write-packet-size
    2015-04-15 13:45:17,946 [main] WARN  org.apache.hadoop.conf.Configuration - dfs.permissions.supergroup is deprecated. Instead, use dfs.permissions.superusergroup
    2015-04-15 13:45:17,946 [main] WARN  org.apache.hadoop.conf.Configuration - topology.script.number.args is deprecated. Instead, use net.topology.script.number.args
    2015-04-15 13:45:17,946 [main] WARN  org.apache.hadoop.conf.Configuration - dfs.umaskmode is deprecated. Instead, use fs.permissions.umask-mode
    2015-04-15 13:45:17,946 [main] WARN  org.apache.hadoop.conf.Configuration - dfs.secondary.http.address is deprecated. Instead, use dfs.namenode.secondary.http-address
    2015-04-15 13:45:17,946 [main] WARN  org.apache.hadoop.conf.Configuration - fs.checkpoint.period is deprecated. Instead, use dfs.namenode.checkpoint.period
    2015-04-15 13:45:17,946 [main] WARN  org.apache.hadoop.conf.Configuration - topology.node.switch.mapping.impl is deprecated. Instead, use net.topology.node.switch.mapping.impl
    2015-04-15 13:45:17,947 [main] WARN  org.apache.hadoop.conf.Configuration - io.bytes.per.checksum is deprecated. Instead, use dfs.bytes-per-checksum
    2015-04-15 13:45:18,011 [main] WARN  org.apache.hadoop.conf.Configuration - fs.default.name is deprecated. Instead, use fs.defaultFS
    2015-04-15 13:45:18,012 [main] WARN  org.apache.hadoop.conf.Configuration - dfs.https.address is deprecated. Instead, use dfs.namenode.https-address
    2015-04-15 13:45:18,013 [main] WARN  org.apache.hadoop.conf.Configuration - io.bytes.per.checksum is deprecated. Instead, use dfs.bytes-per-checksum
    2015-04-15 13:45:18,099 [main] WARN  org.apache.hadoop.conf.Configuration - fs.default.name is deprecated. Instead, use fs.defaultFS
    2015-04-15 13:45:18,100 [main] WARN  org.apache.hadoop.conf.Configuration - dfs.https.address is deprecated. Instead, use dfs.namenode.https-address
    2015-04-15 13:45:18,100 [main] WARN  org.apache.hadoop.conf.Configuration - io.bytes.per.checksum is deprecated. Instead, use dfs.bytes-per-checksum
    2015-04-15 13:45:18,120 [main] WARN  org.apache.hadoop.conf.Configuration - fs.default.name is deprecated. Instead, use fs.defaultFS
    2015-04-15 13:45:18,121 [main] WARN  org.apache.hadoop.conf.Configuration - dfs.https.address is deprecated. Instead, use dfs.namenode.https-address
    2015-04-15 13:45:18,121 [main] WARN  org.apache.hadoop.conf.Configuration - io.bytes.per.checksum is deprecated. Instead, use dfs.bytes-per-checksum
    2015-04-15 13:45:18,139 [main] WARN  org.apache.hadoop.conf.Configuration - fs.default.name is deprecated. Instead, use fs.defaultFS
    2015-04-15 13:45:18,141 [main] WARN  org.apache.hadoop.conf.Configuration - dfs.https.address is deprecated. Instead, use dfs.namenode.https-address
    2015-04-15 13:45:18,142 [main] WARN  org.apache.hadoop.conf.Configuration - io.bytes.per.checksum is deprecated. Instead, use dfs.bytes-per-checksum
    2015-04-15 13:45:18,161 [main] WARN  org.apache.hadoop.conf.Configuration - fs.default.name is deprecated. Instead, use fs.defaultFS
    2015-04-15 13:45:18,162 [main] WARN  org.apache.hadoop.conf.Configuration - dfs.https.address is deprecated. Instead, use dfs.namenode.https-address
    2015-04-15 13:45:18,163 [main] WARN  org.apache.hadoop.conf.Configuration - io.bytes.per.checksum is deprecated. Instead, use dfs.bytes-per-checksum
    2015-04-15 13:45:18,182 [main] WARN  org.apache.hadoop.conf.Configuration - fs.default.name is deprecated. Instead, use fs.defaultFS
    2015-04-15 13:45:18,182 [main] WARN  org.apache.hadoop.conf.Configuration - dfs.https.address is deprecated. Instead, use dfs.namenode.https-address
    2015-04-15 13:45:18,184 [main] WARN  org.apache.hadoop.conf.Configuration - io.bytes.per.checksum is deprecated. Instead, use dfs.bytes-per-checksum
    grunt> DATA = LOAD 'accumulo://example?instance=accumulo&user=root&password=cloudera&zookeepers=127.0.0.1:2181' USING org.apache.accumulo.pig.AccumuloStorage() AS (row,cf,cq,cv,ts,val);
    grunt> DATA2 = FOREACH DATA GENERATE row,cf,cq,cv,val;
    grunt> STORE DATA2 INTO 'accumulo://example_stored?instance=accumulo&user=root&password=cloudera&zookeepers=127.0.0.1:2181' using org.apache.accumulo.pig.AccumuloStorage();
    2015-04-15 13:46:01,543 [main] INFO  org.apache.pig.tools.pigstats.ScriptState - Pig features used in the script: UNKNOWN
    2015-04-15 13:46:01,604 [main] INFO  org.apache.pig.newplan.logical.rules.ColumnPruneVisitor - Columns pruned for DATA: $4
    2015-04-15 13:46:01,780 [main] INFO  org.apache.zookeeper.ZooKeeper - Client environment:zookeeper.version=3.4.5-cdh4.7.0--1, built on 05/28/2014 16:33 GMT
    2015-04-15 13:46:01,780 [main] INFO  org.apache.zookeeper.ZooKeeper - Client environment:host.name=localhost.localdomain
    2015-04-15 13:46:01,780 [main] INFO  org.apache.zookeeper.ZooKeeper - Client environment:java.version=1.6.0_31
    2015-04-15 13:46:01,780 [main] INFO  org.apache.zookeeper.ZooKeeper - Client environment:java.vendor=Sun Microsystems Inc.
    2015-04-15 13:46:01,781 [main] INFO  org.apache.zookeeper.ZooKeeper - Client environment:java.home=/usr/java/jdk1.6.0_31/jre
    *<snip long line>*
    2015-04-15 13:46:01,808 [main] INFO  org.apache.zookeeper.ZooKeeper - Client environment:java.library.path=/usr/lib/hadoop/lib/native
    2015-04-15 13:46:01,808 [main] INFO  org.apache.zookeeper.ZooKeeper - Client environment:java.io.tmpdir=/tmp
    2015-04-15 13:46:01,808 [main] INFO  org.apache.zookeeper.ZooKeeper - Client environment:java.compiler=<NA>
    2015-04-15 13:46:01,808 [main] INFO  org.apache.zookeeper.ZooKeeper - Client environment:os.name=Linux
    2015-04-15 13:46:01,808 [main] INFO  org.apache.zookeeper.ZooKeeper - Client environment:os.arch=amd64
    2015-04-15 13:46:01,808 [main] INFO  org.apache.zookeeper.ZooKeeper - Client environment:os.version=2.6.32-431.17.1.el6.x86_64
    2015-04-15 13:46:01,808 [main] INFO  org.apache.zookeeper.ZooKeeper - Client environment:user.name=root
    2015-04-15 13:46:01,808 [main] INFO  org.apache.zookeeper.ZooKeeper - Client environment:user.home=/root
    2015-04-15 13:46:01,808 [main] INFO  org.apache.zookeeper.ZooKeeper - Client environment:user.dir=/home/cloudera
    2015-04-15 13:46:01,809 [main] INFO  org.apache.zookeeper.ZooKeeper - Initiating client connection, connectString=127.0.0.1:2181 sessionTimeout=30000 watcher=org.apache.accumulo.fate.zookeeper.ZooSession$ZooWatcher@40d6c07
    2015-04-15 13:46:01,835 [main-SendThread(localhost.localdomain:2181)] INFO  org.apache.zookeeper.ClientCnxn - Opening socket connection to server localhost.localdomain/127.0.0.1:2181. Will not attempt to authenticate using SASL (java.lang.SecurityException: Unable to locate a login configuration)
    2015-04-15 13:46:01,836 [main-SendThread(localhost.localdomain:2181)] INFO  org.apache.zookeeper.ClientCnxn - Socket connection established to localhost.localdomain/127.0.0.1:2181, initiating session
    2015-04-15 13:46:01,845 [main-SendThread(localhost.localdomain:2181)] INFO  org.apache.zookeeper.ClientCnxn - Session establishment complete on server localhost.localdomain/127.0.0.1:2181, sessionid = 0x14cbe9d30460010, negotiated timeout = 30000
    2015-04-15 13:46:02,156 [main] INFO  org.apache.pig.backend.hadoop.executionengine.mapReduceLayer.MRCompiler - File concatenation threshold: 100 optimistic? false
    2015-04-15 13:46:02,187 [main] INFO  org.apache.pig.backend.hadoop.executionengine.mapReduceLayer.MultiQueryOptimizer - MR plan size before optimization: 1
    2015-04-15 13:46:02,187 [main] INFO  org.apache.pig.backend.hadoop.executionengine.mapReduceLayer.MultiQueryOptimizer - MR plan size after optimization: 1
    2015-04-15 13:46:02,292 [main] INFO  org.apache.pig.tools.pigstats.ScriptState - Pig script settings are added to the job
    2015-04-15 13:46:02,370 [main] INFO  org.apache.pig.backend.hadoop.executionengine.mapReduceLayer.JobControlCompiler - mapred.job.reduce.markreset.buffer.percent is not set, set to default 0.3
    2015-04-15 13:46:02,381 [main] INFO  org.apache.pig.backend.hadoop.executionengine.mapReduceLayer.JobControlCompiler - Using reducer estimator: org.apache.pig.backend.hadoop.executionengine.mapReduceLayer.InputSizeReducerEstimator
    2015-04-15 13:46:02,382 [main] INFO  org.apache.pig.backend.hadoop.executionengine.mapReduceLayer.InputSizeReducerEstimator - BytesPerReducer=1000000000 maxReducers=999 totalInputFileSize=-1
    2015-04-15 13:46:02,382 [main] INFO  org.apache.pig.backend.hadoop.executionengine.mapReduceLayer.JobControlCompiler - Could not estimate number of reducers and no requested or default parallelism set. Defaulting to 1 reducer.
    2015-04-15 13:46:02,382 [main] INFO  org.apache.pig.backend.hadoop.executionengine.mapReduceLayer.JobControlCompiler - Setting Parallelism to 1
    2015-04-15 13:46:02,684 [main] INFO  org.apache.pig.backend.hadoop.executionengine.mapReduceLayer.JobControlCompiler - creating jar file Job3317770822068456804.jar
    2015-04-15 13:46:06,265 [main] INFO  org.apache.pig.backend.hadoop.executionengine.mapReduceLayer.JobControlCompiler - jar file Job3317770822068456804.jar created
    2015-04-15 13:46:06,282 [main] INFO  org.apache.pig.backend.hadoop.executionengine.mapReduceLayer.JobControlCompiler - Setting up single store job
    2015-04-15 13:46:06,293 [main] INFO  org.apache.pig.data.SchemaTupleFrontend - Key [pig.schematuple] is false, will not generate code.
    2015-04-15 13:46:06,293 [main] INFO  org.apache.pig.data.SchemaTupleFrontend - Starting process to move generated code to distributed cacche
    2015-04-15 13:46:06,293 [main] INFO  org.apache.pig.data.SchemaTupleFrontend - Setting key [pig.schematuple.classes] with classes to deserialize []
    2015-04-15 13:46:06,329 [main] INFO  org.apache.pig.backend.hadoop.executionengine.mapReduceLayer.MapReduceLauncher - 1 map-reduce job(s) waiting for submission.
    2015-04-15 13:46:06,365 [JobControl] WARN  org.apache.hadoop.mapred.JobClient - Use GenericOptionsParser for parsing the arguments. Applications should implement Tool for the same.
    2015-04-15 13:46:06,546 [JobControl] WARN  org.apache.hadoop.conf.Configuration - fs.default.name is deprecated. Instead, use fs.defaultFS
    2015-04-15 13:46:06,547 [JobControl] WARN  org.apache.hadoop.conf.Configuration - dfs.https.address is deprecated. Instead, use dfs.namenode.https-address
    2015-04-15 13:46:06,547 [JobControl] WARN  org.apache.hadoop.conf.Configuration - io.bytes.per.checksum is deprecated. Instead, use dfs.bytes-per-checksum
    2015-04-15 13:46:06,783 [JobControl] INFO  org.apache.pig.backend.hadoop.executionengine.util.MapRedUtil - Total input paths (combined) to process : 1
    2015-04-15 13:46:06,832 [main] INFO  org.apache.pig.backend.hadoop.executionengine.mapReduceLayer.MapReduceLauncher - 0% complete
    2015-04-15 13:46:07,999 [main] INFO  org.apache.pig.backend.hadoop.executionengine.mapReduceLayer.MapReduceLauncher - HadoopJobId: job_201504151245_0007
    2015-04-15 13:46:08,000 [main] INFO  org.apache.pig.backend.hadoop.executionengine.mapReduceLayer.MapReduceLauncher - Processing aliases DATA,DATA2
    2015-04-15 13:46:08,000 [main] INFO  org.apache.pig.backend.hadoop.executionengine.mapReduceLayer.MapReduceLauncher - detailed locations: M: DATA[1,7],DATA2[-1,-1] C:  R:
    2015-04-15 13:46:08,000 [main] INFO  org.apache.pig.backend.hadoop.executionengine.mapReduceLayer.MapReduceLauncher - More information at: http://localhost.localdomain:50030/jobdetails.jsp?jobid=job_201504151245_0007
    2015-04-15 13:46:17,117 [main] INFO  org.apache.pig.backend.hadoop.executionengine.mapReduceLayer.MapReduceLauncher - 50% complete
    2015-04-15 13:46:22,706 [main] INFO  org.apache.pig.backend.hadoop.executionengine.mapReduceLayer.MapReduceLauncher - 100% complete
    2015-04-15 13:46:22,710 [main] INFO  org.apache.pig.tools.pigstats.SimplePigStats - Script Statistics:

    HadoopVersion	PigVersion	UserId	StartedAt	FinishedAt	Features
    2.0.0-cdh4.7.0	0.11.0-cdh4.7.0	root	2015-04-15 13:46:02	2015-04-15 13:46:22	UNKNOWN

    Success!

    Job Stats (time in seconds):
    JobId	Maps	Reduces	MaxMapTime	MinMapTIme	AvgMapTime	MedianMapTime	MaxReduceTime	MinReduceTime	AvgReduceTime	MedianReducetime	Alias	Feature	Outputs
    job_201504151245_0007	1	0	3	3	3	3	0	0	0	0	DATA,DATA2	MAP_ONLY	accumulo://example_stored?instance=accumulo&user=root&password=cloudera&zookeepers=127.0.0.1:2181,

    Input(s):
    Successfully read 4 records (534 bytes) from: "accumulo://example?instance=accumulo&user=root&password=cloudera&zookeepers=127.0.0.1:2181"

    Output(s):
    Successfully stored 4 records in: "accumulo://example_stored?instance=accumulo&user=root&password=cloudera&zookeepers=127.0.0.1:2181"

    Counters:
    Total records written : 4
    Total bytes written : 0
    Spillable Memory Manager spill count : 0
    Total bags proactively spilled: 0
    Total records proactively spilled: 0

    Job DAG:
    job_201504151245_0007


    2015-04-15 13:46:22,724 [main] INFO  org.apache.pig.backend.hadoop.executionengine.mapReduceLayer.MapReduceLauncher - Success!



# Local Hadoop cluster

First af all run docker-compose.yml in its directory.
> **docker-compose up**
> Starting dockerhive_presto-coordinator_1 ... 
Starting dockerhive_namenode_1 ... 
Starting dockerhive_presto-coordinator_1
Starting dockerhive_hive-server_1 ... 
Starting dockerhive_namenode_1
Starting dockerhive_hive-metastore-postgresql_1 ... 
Starting dockerhive_hive-server_1
Starting dockerhive_datanode_1 ... 
Starting dockerhive_hive-metastore-postgresql_1
Starting dockerhive_datanode_1
Starting dockerhive_hive-metastore_1 ... 
Starting dockerhive_hive-metastore-postgresql_1 ... done

Than check the running containers.
> **docker ps** 
CONTAINER ID                  PORTS                                            NAMES
f69fc25be358                  0.0.0.0:8080->8080/tcp                           dockerhive_presto-coordinator_1
491f5afd620c                  0.0.0.0:50070->50070/tcp                         dockerhive_namenode_1
50eebbd7bc00                  0.0.0.0:10000->10000/tcp, 10002/tcp              dockerhive_hive-server_1
9af454572c40                  5432/tcp                                         dockerhive_hive-metastore-postgresql_1
f69fc25be358                  10000/tcp, 0.0.0.0:9083->9083/tcp, 10002/tcp     dockerhive_hive-metastore_1
856d4ff56f02                  0.0.0.0:50075->50075/tcp                         dockerhive_datanode_1

## HDFS
Create a directory for input data.
> **hdfs dfs -mkdir /input**
>  **hdfs dfs -ls /**
Found 3 items
drwxr-xr-x   - root supergroup          0 2019-11-11 09:46 /input
drwxrwxr-x   - root supergroup          0 2019-11-07 09:32 /tmp
drwxr-xr-x   - root supergroup          0 2019-11-07 09:32 /user

Puit data into it.
> **hdfs dfs -put /input/MOCK_DATA.csv /input/MOCK_DATA.txt**
> **hdfs dfs -ls /input**
Found 1 items
-rw-r--r--   3 root supergroup      62144 2019-11-11 09:46 /input/MOCK_DATA.csv

Show data in a file.
> **hdfs dfs -cat /input/MOCK_DATA.txt**
2019-11-11 09:50:06,390 INFO sasl.SaslDataTransferClient: SASL encryption trust check: localHostTrusted = false, remoteHostTrusted = false
id,first_name,last_name,email,gender,ip_address
1,Hoebart,Shrieve,hshrieve0@dailymotion.com,Male,135.190.152.189
2,Darby,Husby,dhusby1@guardian.co.uk,Male,194.247.107.96
3,Adan,O'Scandall,aoscandall2@google.co.jp,Male,153.24.114.201
4,Woodrow,Bosket,wbosket3@studiopress.com,Male,11.160.65.68
5,Elke,Sanbroke,esanbroke4@ucla.edu,Female,143.216.157.83
6,Shane,Aaronson,saaronson5@stumbleupon.com,Female,141.162.80.13
7,Harcourt,Arman,harman6@printfriendly.com,Male,20.113.154.213
8,Isiahi,Bachanski,ibachanski7@live.com,Male,215.83.158.130
9,Thia,Broxholme,tbroxholme8@sciencedirect.com,Female,187.51.32.3
10,Solly,Erley,serley9@trellian.com,Male,71.150.18.173

Copy to local.
> **hdfs dfs -copyToLocal input/MOCK_DATA.txt hadoop-data/fromHdfs.txt**
2019-10-31 14:43:30,381 INFO sasl.SaslDataTransferClient: SASL encryption trust check: localHostTrusted = false, remoteHostTrusted = false
**ls hadoop-data/**
MOCK_DATA.csv  fromHdfs.csv


## Hive
Show list of databases: 
> hive> **show databases;**
OK
default
Time taken: 0.047 seconds, Fetched: 1 row(s)

Create database:
>hive> **create database training;**
OK
Time taken: 1.138 seconds

Use database:
> hive> **use training;**
OK
Time taken: 0.98 seconds

Show list of tables in database:
> hive> **show tables;**
OK
Time taken: 0.304 seconds

Create table:
> hive> **CREATE TABLE IF NOT EXISTS users (id int, firstName String, secondName String)ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' STORED AS TEXTFILE;**
OK
Time taken: 0.282 seconds

Show info about the table:
> hive> **describe users;**
OK
id                      int                                         
firstname               string                                      
secondname              string                                      
Time taken: 0.489 seconds, Fetched: 3 row(s)
hive> **describe extended users;**
OK
id                      int                                         
firstname               string                                      
secondname           string                                      
Show more detailed info about the table:       
Detailed Table Information    Table(tableName:users, dbName:training, owner:root, createTime:1572938947, lastAccessTime:0, retention:0, sd:StorageDescriptor(cols:[FieldSchema(name:id, type:int, comment:null), FieldSchema(name:firstname, type:string, comment:null), FieldSchema(name:secondname, type:string, comment:null)], location:hdfs://namenode:8020/user/hive/warehouse/training.db/users, inputFormat:org.apache.hadoop.mapred.TextInputFormat, outputFormat:org.apache.hadoop.hive.ql.io.HiveIgnoreKeyTextOutputFormat, compressed:false, numBuckets:-1, serdeInfo:SerDeInfo(name:null, serializationLib:org.apache.hadoop.hive.serde2.lazy.LazySimpleSerDe, parameters:{serialization.format=1}), bucketCols:[], sortCols:[], parameters:{}, skewedInfo:SkewedInfo(skewedColNames:[], skewedColValues:[], skewedColValueLocationMaps:{}), storedAsSubDirectories:false), partitionKeys:[], parameters:{totalSize=0, rawDataSize=0, numRows=0, COLUMN_STATS_ACCURATE={"BASIC_STATS":"true"}, numFiles=0, transient_lastDdlTime=1572938947}, viewOriginalText:null, viewExpandedText:null, tableType:MANAGED_TABLE, rewriteEnabled:false)    
Time taken: 2.99 seconds, Fetched: 5 row(s)

Load data into a table:
> hive> **LOAD DATA INPATH "/input/MOCK_DATA.txt" into table users;**
Loading data to table training.users
OK
Time taken: 4.204 seconds

Check this data into a table.
> hive> **select * from users;**
OK
1     "Ulla"     "Penhalewick"
2     "Eldredge"     "Osgardby"
3     "Aurelia"     "Whalley"
4     "Heida"     "Corney"
5     "Kele"     "Pillington"
6     "Oona"     "Berth"
7     "Garland"     "Gremane"
8     "Merrie"     "Manderson"
9     "Aurilia"     "Fillary"
10     "Opalina"     "Dunabie"
11     "Leroi"     "Phillcock"
12     "Pattie"     "Dowtry"
13     "Trude"     "Lieber"
14     "Matthieu"     "Goodin"
15     "Allene"     "Sockell"
16     "Juan"     "McIlmurray"
17     "Holt"     "Wayne"
18     "Terese"     "McClary"
19     "Ad"     "Hodgen"
20     "Irwinn"     "Seville"
Time taken: 0.202 seconds, Fetched: 20 row(s)


## Presto
In presto container create etc/catalog/hive.properties with the following contents to mount the hive-hadoop2 connector as the hive catalog, replacing example:9083 with the correct host and port for your Hive metastore Thrift service:
> connector.name=hive-hadoop2
hive.metastore.uri=thrift://example.net:9083 (f69fc25be358:9083)

Restart container.

Show list of catalogs:
> **/presto-cli --server localhost:8080;**
presto> show catalogs;
  Catalog  
 blackhole 
 hive      
 jmx       
 memory    
 system    
 tpcds     
 tpch      
(7 rows)
Query 20191111_135714_00000_uja4c, FINISHED, 1 node
Splits: 19 total, 19 done (100.00%)
0:01 [0 rows, 0B] [0 rows/s, 0B/s]

Show list of schemas:
> **./presto-cli --server localhost:8080 --catalog hive --schema training;**
presto:training> show schemas;
  Schema       
 default            
 information_schema 
 training           
(3 rows)

Show data from table users:
> presto:training> **select * from users;**
  id  |   firstname    |      secondname       
------+----------------+-----------------------
 NULL | first_name     | last_name             
    1 | Hoebart        | Shrieve               
    2 | Darby          | Husby                 
    3 | Adan           | O'Scandall            
    4 | Woodrow        | Bosket                
    5 | Elke           | Sanbroke              
    6 | Shane          | Aaronson              
    7 | Harcourt       | Arman                 
    8 | Isiahi         | Bachanski             
    9 | Thia           | Broxholme             
   10 | Solly          | Erley                 
   11 | Krystle        | Waycot                
   12 | Gael           | Uttley                
   13 | Fin            | Galtone               
   14 | Cherianne      | Beaglehole            
   15 | Myrwyn         | De la Eglise          
   16 | Esme           | Pauluzzi              
   17 | Nichole        | Mutimer               
   18 | Katrine        | Titchard              
   19 | Danell         | O'Sheeryne            
   20 | Rudiger        | Pappin                
   21 | Myrvyn         | Rogans                
   22 | Rowland        | Wallach               
   23 | Rikki          | Wankling              
   24 | Charmine       | Quest                 
   25 | Findlay        | Burnet                
   26 | Gage           | Warman                
   27 | Ceciley        | Ames                  
   28 | Jourdan        | Brumfield             
   29 | Rolf           | Buey                  
   30 | Carmen         | Shoveller
   :
Query 20191111_135919_00005_uja4c, FINISHED, 1 node
Splits: 17 total, 17 done (100.00%)
0:02 [1K rows, 60.7KB] [624 rows/s, 37.8KB/s]




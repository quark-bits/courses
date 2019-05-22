## Exercise 2 – CQL

For Cassandra, I've used docker based instance.
1. Download & install docker.
2. Install Cassandra through kitematic or commandline.
3. Start the cassandra docker instance.
4. Connect to  cassandra docker shell.


### Exercises
We'll execute CQLs through cqlsh. From docker shell, execute 'cqlsh'

###### 3. Create KeySpace   
    cqlsh> CREATE KEYSPACE killrvideo
    WITH REPLICATION = {
      'class': 'SimpleStrategy',
      'replication_factor': 1
    };

###### 4. Use KeySpace
    cqlsh> USE killrvideo;
This will avoid specifying keyspace name when executing CQLs.

###### 5. Create Table
    cqlsh:killrvideo> CREATE TABLE videos (
        video_id timeuuid,
        added_date timestamp,
        Title Text,
        PRIMARY KEY (video_id)
    );
    
###### 6. Insert into Table
    cqlsh:killrvideo> INSERT INTO videos(video_id, added_date, title)
    VALUES (1645ea59-14bd-11e5-a993-8138354b7e31,'2014-01-29', 'Cassandra History'); 
    

###### 7. Select 
    cqlsh:killrvideo> SELECT * FROM videos;


###### 8. Insert 2nd record Table
    cqlsh:killrvideo> INSERT INTO videos(video_id, added_date, title)
    VALUES (245e8024-14bd-11e5-9743-8238356b7e32,'2012-04-03', 'Cassandra & SSDs'); 
        

###### 9. Truncate Table
    cqlsh:killrvideo> TRUNCATE videos;
    

###### 10. Import data
As we're using docker based instance, first we'll copy the data file (videos.csv) into docker instance.

* View the cassandra containerId
     
        # docker ps
        CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS                                                                                                                         NAMES
        749fa86bd304        cassandra:latest    "docker-entrypoint.s…"   20 hours ago        Up 20 hours         0.0.0.0:32782->7000/tcp, 0.0.0.0:32781->7001/tcp, 0.0.0.0:32780->7199/tcp
   
* Copy the csv file   
          
        # docker cp ~/Downloads/videos.csv 749fa86bd304:/tmp   
    
* Import Data 

        cqlsh:killrvideo> COPY videos(video_id,added_date,title) FROM '/tmp/videos.csv' WITH HEADER=TRUE;
        
        //Note: HEADER=TRUE, indicates first line in csv has column names.
        
        
###### 12. Select count of imported rows.

    cqlsh:killrvideo> SELECT count(*) FROM videos;
    

## Problems/Issues
#### Not in any keyspace:
This happens when we have not specified the KeySpace.

E.g
    
    cqlsh> COPY videos(video_id,added_date,title) FROM '/tmp/videos.csv' WITH HEADER=TRUE;
    Not in any keyspace.

    
We can specify the KeySpaceName while using the query. 

    cqlsh> COPY killrvideo.videos(video_id,added_date,title) FROM '/tmp/videos.csv' WITH HEADER=TRUE;
    Not in any keyspace.

Alternatively, if we set keyspace with USE command, then cqlsh will show the active keyspace.

    cqlsh> USE killrvideo;        
    cqlsh:killrvideo>
    

#### Warnings: Aggregation query used without partition key
When we execute Aggregation CQLs without specifying the partition key, it could lead to performance issue as coordinator has to scan all rows 
for that table across all nodes. 

    cqlsh:killrvideo> SELECT count(*) FROM videos;    
    

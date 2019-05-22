## Exercise 3 – Partitions

### Exercises
We'll execute CQLs through cqlsh. From docker shell, execute 'cqlsh'

###### 3) Execute the following command to view the metadata for the videos table you created earlier.

      cqlsh:killrvideo> DESCRIBE TABLE videos;   
      
      output:      
               
      CREATE TABLE killrvideo.videos (
          video_id timeuuid PRIMARY KEY,
          added_date timestamp,
          title text
      ) WITH bloom_filter_fp_chance = 0.01
          AND caching = {'keys': 'ALL', 'rows_per_partition': 'NONE'}
          AND comment = ''
          AND compaction = {'class': 'org.apache.cassandra.db.compaction.SizeTieredCompactionStrategy', 'max_threshold': '32', 'min_threshold': '4'}
          AND compression = {'chunk_length_in_kb': '64', 'class': 'org.apache.cassandra.io.compress.LZ4Compressor'}
          AND crc_check_chance = 1.0
          AND dclocal_read_repair_chance = 0.1
          AND default_time_to_live = 0
          AND gc_grace_seconds = 864000
          AND max_index_interval = 2048
          AND memtable_flush_period_in_ms = 0
          AND min_index_interval = 128
          AND read_repair_chance = 0.0
          AND speculative_retry = '99PERCENTILE';
        
    
    What is the partition key?
        video_id.

    How many partitions are in this table?
        One per unique video_id.
        

###### 4) Execute the following query to view the partitioner token value for each video id.

    cqlsh:killrvideo> SELECT token(video_id), video_id FROM videos;
    
    output:        
     system.token(video_id) | video_id
    ------------------------+--------------------------------------
       -7805440677194688247 | 245e8024-14bd-11e5-9743-8238356b7e32
       -1963973032031712291 | 3452f7de-14bd-11e5-855e-8738355b7e3a
       -1613479371119279545 | 5645f8bd-14bd-11e5-af1a-8638355b8e3a
        3855558958565172223 | 1645ea59-14bd-11e5-a993-8138354b7e31
        7966101712501124149 | 4845ed97-14bd-11e5-8a40-8338255b7e33
        

###### 7) Your mission, should you choose to accept it, is to write a CREATE TABLE statement that will store this data partitioned by tags. With this given data set, there should be two partitions, one for each tag. Call your table videos_by_tag.
     cqlsh:killrvideo> CREATE TABLE videos_by_tag (
                               tag Text, 
                               video_id timeuuid,
                               added_date timestamp,
                               Title Text,
                               PRIMARY KEY ((tag), video_id)
                           );
                           
    cqlsh:killrvideo> DESCRIBE videos_by_tag;
    
    CREATE TABLE killrvideo.videos_by_tag (
        tag text,
        video_id timeuuid,
        added_date timestamp,
        title text,
        PRIMARY KEY (tag, video_id)
    ) WITH CLUSTERING ORDER BY (video_id ASC)
        AND bloom_filter_fp_chance = 0.01
        AND caching = {'keys': 'ALL', 'rows_per_partition': 'NONE'}
        AND comment = ''
        AND compaction = {'class': 'org.apache.cassandra.db.compaction.SizeTieredCompactionStrategy', 'max_threshold': '32', 'min_threshold': '4'}
        AND compression = {'chunk_length_in_kb': '64', 'class': 'org.apache.cassandra.io.compress.LZ4Compressor'}
        AND crc_check_chance = 1.0
        AND dclocal_read_repair_chance = 0.1
        AND default_time_to_live = 0
        AND gc_grace_seconds = 864000
        AND max_index_interval = 2048
        AND memtable_flush_period_in_ms = 0
        AND min_index_interval = 128
        AND read_repair_chance = 0.0
        AND speculative_retry = '99PERCENTILE';                           
                           

###### 8) Execute the following COPY command to import the videos-by-tag.csv data.
Note: Use "docker cp" to copy file into cassandra docker instance.

    cqlsh:killrvideo> COPY videos_by_tag(tag, video_id, added_date, title) FROM '/tmp/videos-by-tag.csv' WITH HEADER=TRUE;
    
###### 9) Verify CQL imported your data correctly by writing a SELECT * command.
    cqlsh:killrvideo> SELECT * FROM videos_by_tag;     
    
    output:
    tag       | video_id                             | added_date                      | title
    -----------+--------------------------------------+---------------------------------+------------------------------
      datastax | 4845ed97-14bd-11e5-8a40-8338255b7e33 | 2013-10-16 00:00:00.000000+0000 |              DataStax Studio
      datastax | 5645f8bd-14bd-11e5-af1a-8638355b8e3a | 2013-04-16 00:00:00.000000+0000 | What is DataStax Enterprise?
     cassandra | 1645ea59-14bd-11e5-a993-8138354b7e31 | 2014-01-29 00:00:00.000000+0000 |            Cassandra History
     cassandra | 245e8024-14bd-11e5-9743-8238356b7e32 | 2012-04-03 00:00:00.000000+0000 |             Cassandra & SSDs
     cassandra | 3452f7de-14bd-11e5-855e-8738355b7e3a | 2013-03-17 00:00:00.000000+0000 |              Cassandra Intro
    (5 rows)                            

###### 10) Write a SELECT statement to retrieve all rows tagged with cassandra.

    cqlsh:killrvideo> SELECT * FROM videos_by_tag WHERE tag = 'cassandra';
    
     tag       | video_id                             | added_date                      | title
    -----------+--------------------------------------+---------------------------------+-------------------
     cassandra | 1645ea59-14bd-11e5-a993-8138354b7e31 | 2014-01-29 00:00:00.000000+0000 | Cassandra History
     cassandra | 245e8024-14bd-11e5-9743-8238356b7e32 | 2012-04-03 00:00:00.000000+0000 |  Cassandra & SSDs
     cassandra | 3452f7de-14bd-11e5-855e-8738355b7e3a | 2013-03-17 00:00:00.000000+0000 |   Cassandra Intro
     (3 rows)
           
###### 11) Now, find all videos tagged with datastax (similar to the previous query).

    cqlsh:killrvideo> SELECT * FROM videos_by_tag WHERE tag = 'datastax';
    
     tag      | video_id                             | added_date                      | title
    ----------+--------------------------------------+---------------------------------+------------------------------
     datastax | 4845ed97-14bd-11e5-8a40-8338255b7e33 | 2013-10-16 00:00:00.000000+0000 |              DataStax Studio
     datastax | 5645f8bd-14bd-11e5-af1a-8638355b8e3a | 2013-04-16 00:00:00.000000+0000 | What is DataStax Enterprise?
    
    (2 rows)                                              
    
###### 12) Finally, write a query to retrieve the video having a title of Cassandra Intro.
        
        cqlsh:killrvideo> SELECT * FROM videos_by_tag WHERE title = 'Cassandra Intro';
            InvalidRequest: Error from server: code=2200 [Invalid query] message="Cannot execute this query as it might involve data filtering and thus may have unpredictable performance. If you want to execute this query despite the performance unpredictability, use ALLOW FILTERING"        
            
As "title" is not a PartitionKey/Indexed, cassandra has to do full-scan to filter title.

## Problems/Issues
#### code=2200 [Invalid query]

    cqlsh:killrvideo> SELECT * FROM videos_by_tag WHERE title = 'Cassandra Intro';
    InvalidRequest: Error from server: code=2200 [Invalid query] message="Cannot execute this query as it might involve data filtering and thus may have unpredictable performance. If you want to execute this query despite the performance unpredictability, use ALLOW FILTERING"        
    
Refer: https://www.datastax.com/dev/blog/allow-filtering-explained-2    
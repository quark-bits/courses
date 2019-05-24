## Exercise 4 – Clustering Columns

### Exercises

######  3) Modify the following command to create a new videos_by_tag table partitioned based on the tag. The table should also store the rows of each partition so that the newest videos are listed first within the partition.
    
    cqlsh:killrvideo> CREATE TABLE videos_by_tag (
         tag text,
         video_id uuid,
         added_date timestamp,
         title text,
         PRIMARY KEY ((tag), added_date, video_id))
         WITH CLUSTERING ORDER BY(added_date DESC);
         

######  5) Perform a SELECT * query on videos_by_tag.

    cqlsh:killrvideo> SELECT * FROM videos_by_tag ;
    
     tag       | added_date                      | video_id                             | title
    -----------+---------------------------------+--------------------------------------+------------------------------
      datastax | 2013-10-16 00:00:00.000000+0000 | 4845ed97-14bd-11e5-8a40-8338255b7e33 |              DataStax Studio
      datastax | 2013-04-16 00:00:00.000000+0000 | 5645f8bd-14bd-11e5-af1a-8638355b8e3a | What is DataStax Enterprise?
     cassandra | 2014-01-29 00:00:00.000000+0000 | 1645ea59-14bd-11e5-a993-8138354b7e31 |            Cassandra History
     cassandra | 2013-03-17 00:00:00.000000+0000 | 3452f7de-14bd-11e5-855e-8738355b7e3a |              Cassandra Intro
     cassandra | 2012-04-03 00:00:00.000000+0000 | 245e8024-14bd-11e5-9743-8238356b7e32 |             Cassandra & SSDs
     
     
######  6) Execute your query again, but list the oldest videos first.     
    cqlsh:killrvideo>  SELECT * FROM videos_by_tag ORDER BY added_date ASC;
    InvalidRequest: Error from server: code=2200 [Invalid query] message="ORDER BY is only supported when the partition key is restricted by an EQ or an IN."


######  7) Change your query to restrict the partition key value to 'cassandra'.
    
    cqlsh:killrvideo> SELECT * FROM videos_by_tag WHERE tag = 'cassandra' ORDER BY added_date ASC;
    
     tag       | added_date                      | video_id                             | title
    -----------+---------------------------------+--------------------------------------+-------------------
     cassandra | 2012-04-03 00:00:00.000000+0000 | 245e8024-14bd-11e5-9743-8238356b7e32 |  Cassandra & SSDs
     cassandra | 2013-03-17 00:00:00.000000+0000 | 3452f7de-14bd-11e5-855e-8738355b7e3a |   Cassandra Intro
     cassandra | 2014-01-29 00:00:00.000000+0000 | 1645ea59-14bd-11e5-a993-8138354b7e31 | Cassandra History
     
     
######  8) Change your query to retrieve videos made in 2013 or later.
    cqlsh:killrvideo> SELECT * FROM videos_by_tag WHERE tag= 'cassandra' AND added_date>='2013-01-01';
    
     tag       | added_date                      | video_id                             | title
    -----------+---------------------------------+--------------------------------------+-------------------
     cassandra | 2014-01-29 00:00:00.000000+0000 | 1645ea59-14bd-11e5-a993-8138354b7e31 | Cassandra History
     cassandra | 2013-03-17 00:00:00.000000+0000 | 3452f7de-14bd-11e5-855e-8738355b7e3a |   Cassandra Intro
         
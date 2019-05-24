## Exercise 6 – Node

#### 3) Try: ./nodetool status

    # nodetool status
    Datacenter: datacenter1
    =======================
    Status=Up/Down
    |/ State=Normal/Leaving/Joining/Moving
    --  Address     Load       Tokens       Owns (effective)  Host ID                               Rack
    UN  172.17.0.2  306.77 KiB  256          100.0%            ed19c671-f413-4108-b9f2-1bb62ce8975c  rack1


#### 5) Try: ./nodetool info

    # nodetool info
    ID                     : ed19c671-f413-4108-b9f2-1bb62ce8975c
    Gossip active          : true
    Thrift active          : false
    Native Transport active: true
    Load                   : 306.77 KiB
    Generation No          : 1558480238
    Uptime (seconds)       : 44681
    Heap Memory (MB)       : 121.30 / 975.13
    Off Heap Memory (MB)   : 0.00
    Data Center            : datacenter1
    Rack                   : rack1
    Exceptions             : 0
    Key Cache              : entries 25, size 2.12 KiB, capacity 48 MiB, 190 hits, 234 requests, 0.812 recent hit rate, 14400 save period in seconds
    Row Cache              : entries 0, size 0 bytes, capacity 0 bytes, 0 hits, 0 requests, NaN recent hit rate, 0 save period in seconds
    Counter Cache          : entries 0, size 0 bytes, capacity 24 MiB, 0 hits, 0 requests, NaN recent hit rate, 7200 save period in seconds
    Chunk Cache            : entries 20, size 1.25 MiB, capacity 211 MiB, 138 misses, 898 requests, 0.846 recent hit rate, NaN microseconds miss latency
    Percent Repaired       : 100.0%
    Token                  : (invoke with -T/--tokens to see all 256 tokens)
    

#### 6) Try: ./nodetool describecluster    

    nodetool describecluster
    Cluster Information:
        Name: Test Cluster
        Snitch: org.apache.cassandra.locator.SimpleSnitch
        DynamicEndPointSnitch: enabled
        Partitioner: org.apache.cassandra.dht.Murmur3Partitioner
        Schema versions:
            836f4520-071b-3909-91d9-876181702696: [172.17.0.2]
            

#### 7) Try: ./nodetool getlogginglevels

    # nodetool getlogginglevels
    
    Logger Name                                        Log Level
    ROOT                                                    INFO
    com.thinkaurelius.thrift                               ERROR
    org.apache.cassandra                                   DEBUG     
    
    
           
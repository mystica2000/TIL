# Data Replication 
   Why do we need data replication? 
  - Edge (improve latency)
  - Data Security (In case of disaster, backup) 
  - Increased Read throughput

  There are different ways of achieving it on the distributed environment;
- Single-Leader based Replication
- Multi-Leader base Replication
- Leaderless Replication


## Single-Leader based 
  - also called "master-slave/active-passive" replication.
  - There will be a single leader and multiple copies of followers in which writes have to go thru leader and read is performed by any of the followers. 

![image](https://github.com/mystica2000/TIL/assets/45729256/30945ef0-3a2a-4d74-9a81-9b425577bdc2)


There are couple of ways to perform data replication in general;
- Sync
- Async 

Sync -> follower waits for the data replicated to all the follower nodes
Async -> followers just waits till the any one of the followers are replicated and perform others in async manner, by this atleast one node is guarantees to have a replicated data and donot block the leader node from processing any write requests. 

#### Problem with sync 
- what if any one of the followers is down or having poor network?! Then it causes the leader node to wait indefinitely till it comes back up. Async is mostly preferred.


### How to actually perform replication?! 
- Can be done on either application or database level.
- On Database Level, the vendors like postgres, mysql have replication logs that can be streamed to the followers. 
- On Application Level, several edge cases have to be taken into consideration like retries, fault tolerance etc.

#### Setting up new follower without downtime;
- Take snapshot of the leader db
- Copy the snapshot to the follower node
- Follower requests the data changes that happened after the snapshot was taken
- After it gets updated and makes sync with the leader, it can process the requests just like other followers do.


**We will see replication at the database level here with an async method.**

#### What Do Replication Logs Look like?! 

##### Statement based replication 
- Can we just send the sql queries instead to all the followers?! - Answer is NO. 
- Long answer: 
  - The sql functions like NOW(), RAND() have the date/time, random variable when executed it will give out different results on every follower based on when it received the data from the leader.
  - So as the triggers, auto-increments too.

Solution: It has to return a fixed value in place of those functions.

##### Write-Ahead Log (WAL)
- Append only log to make sure of the data integrity, it contains a sequence of bytes containing all the writes to the DB. 
- It is lower-level and tied to the storage engine of the DB, making it not possible to run different versions of the db in the leaders and followers, if having to upgrading all the nodes at the same time, down-time is necessary.

##### Row-based Log replication (Logical Log)
- It sends new values of all the columns (WRITE)
- It sends which column is updated (UPDATE)
- It sends which column got deleted (DELETE)
- It is decoupled from the storage engine.

Mysql has it as BinLog, it is also useful for external applications like storing in data warehouses to be parsed by some other tools, cache etc.

##### Trigger-based Log
- There may be a situation where replication have to be moved up to the application
- Trigger helps to execute custom code/scripts that automatically gets executed when data changes. This log can be entered in a different table to be read by an external process, but it is error prone since handling errors and retries and other edge cases have to be considered.


#### Handling Node Failures: 

##### Follower: Catchup recovery
- What if a follower went down and lost its connection for a while and came back online?! How to make it sync with the leader. 
- Replication Logs have the snapshots associated with them, after the follower comes back online it checks with the leader whether there is any data i missed? If yes, then the follower gets the data until it is the same as the leader.


##### Leader: failover
- What if a leader went down?! 

- Automatic Failover
  - Determining Whether the leader is down or not can be performed by having timeout/heartbeats, if not responded then the leader is considered dead.
  - Since all writes go thru the leader, we have to perform leader selection in which one of the followers will be promoted to the leader.
  - The follower which is promoted to the leader will start receiving the write requests.
  - All other followers are reconfigured to collect the replication log from the newly promoted leader. 

  - Now, What if that leader who went down comes back online?! 
    - It will be reconfigured to step down as a follower. Data loss might occur if that previous leader received a write that didn’t get processed (before the timeout that we declare leader as dead) which might be a serious issue. 

- To avoid data loss, Manual Failover is performed by some teams. 


#### Replication Lag;
Not every follower will receive/process the replication log at the same time of the leader; lag can be from a few seconds to minutes. 

What if I am requesting data from the follower B which has some replication lag and doesn't contain the updated data as of the leader. 

##### Problem with async method; 
  - User A is performing a write operation and wants to read data in which they updated.  Since followers are async, performing read from it might or might not contain the updated data since async, we don’t know when it got updated or not. 

##### Read after write:

  - To avoid this issue, we can go for a method called “read after write”, in which if read is performed after write, then the user query must be handled by the leader which has the newly updated data and for a while let the leader serve the query (ie) 1 minute after timestamp of lastly updated data.  
  - Client which is requesting can send the timestamp and the system will handle the query from the leader. 
  - Cross-device read-after-write has also to be considered if the user requests from the different device. 

##### Monotonic Reads: 
  - There are sometimes, data might go back in time; 
  - **for example**: in the async replication, user might request the data from follower 1 which has the updated data as that of the leader, on the next time; the user might request the data from follower 2 (different data center) which is not updated yet due to replication lag, then it doesn’t show the updated data leading to confusion; 
  - Make sure the user reads the data from the same replica by hashing it with userID or something.

##### Consistent Prefix Reads:
  - If a sequence of the writes happens then on the receiving end, it has to be in the same order. 
  - **for example**: if user A sends “How are you” and user B sends “I'm Fine and you?”, and it got swapped like user B query gets before the user A then it will look meaningless. 

  - This problem happens in the partitioned (shared) db as there is no global ordering of writes.
  - So a sequence of writes which are related to each other have to be performed in the same partition.
       
##### How to solve the replication Lag?!
  - Transactions exist in the Db to provide strong guarantees so that application can be simpler.

Reference: 

https://dataintensive.net/

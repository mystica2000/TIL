# Multi-Leader Replication:
   -	Instead of having a single leader in which all writes must go thru it, it contains multiple-leaders in which it allows multiple nodes to accept writes.
  - Multi-leader (Active-active, master-master) replication

![image](https://github.com/mystica2000/TIL/assets/45729256/6b591bf1-a70c-4702-a98c-746e539773b8)

## Use Case:

### Mutli-data center
   **For example**: In single-leader replication, User A is near to the DataCenter B and performing write but the node (leader) which accepts write is in DataCenter A, then it adds a latency in which user query have to go the data center A and perform write instead of using nearby data center B to perform write.    
   - Multi-leader replication shortens latency by allowing a node in the Data Center B to perform write instead. 


  - It follows the same paradigm as the single-leader in the data-center except for separate leaders. Each leader in the data center replicates with one another. 
  - writes is independent of the datacenter.

| Comparison | Single Leader    | Multi Leader |
| --------- | --------- | ------- |
| Performance & Network | latency since write have to go to a datacenter <br/> where leader is present        | not a problem here since every datacenter is equiped with leader and can handle writes and it is replicated async across multiple data center containing other leaders, so latency is hidden.
|  Outrage         | If leader is down, then perform failover and have one of the follower to be promoted to leader        | If one leader is down, then the user can be redirected to a different data center where leader is not down and continue performing writes, and catch up the data with the other leaders when that leader comes online

BDR (Bi-Directional Replication) in postgres, tungsten replicater in mysql can be configured for multi-leader replication

### Offline Functionality
  - An application that needs offline Functionality and continue to work offline. 
  - In this case, it can have a local db that acts as leader and there is an async multi-leader replication (sync) across multiple devices. 
  - Replication Lags here can be any number since device can be offline for more than day or weeks. 
  - It is similar to multi-data center except it is taken to edge (user's device)
  - Couch DB is designed for this kinda operation. 

### Collaborative Editing
  - Collaborative Editing application - allows several ppl to perform edits on the document like google docs. It is mainly associated with previous use case (offline editing). 

#### With new paradigm, comes new challenges; 
### Write Conflict :- 
   - For example: it’s collaborating app like google docs where data concurrently modified by the users, User A is accessing and performing write from Data center A, User B is accessing and performing write on the same document from Data center B at the same time, now the leader in both data center has different values. Now on performing sync with other leaders, it results in conflict where both leader has different data for the document by the two users A and B.

   - It causes write conflict like whether to go for user A's write or user's B write. 

- It won’t happen in the single-leader replication before, since all writes go thru single write node (leader). 

### Other conflicts :-
  - Integrity Constraints, auto-increment keys across leaders 

## Conflict Resolution:-
   
   The Goal of conflict resolution is "All values across leaders to have a same value at final"

  - Manual 
      - In async replication, the conflict is detected async across leaders in the background. It might be too late to ask the user to manually perform conflict Resolution.
     - Manual - Like Git
  - Automatic
     - One way is to obtain a lock when user A is performing write, user B waits and vice versa. But it doesn't allow for faster collaboration
     - Another is having a timestamp, UUID along with value so that we can sort by desc to get the latest value and ignore all others (late write wins) but prone to data loss (imagine bank details :P)
     - OT - Operational Transformations - google docs. Specifically for list of texts
     - CRDT - Custom Data types that contains sets, lists, counter that can be concurrently edited by multiple users and it resolves automatically. 
  - Conflict Avoidance
     - If a particular record/document is getting edited, then all users are routed to the same leader to avoid having to deal with conflict.
  - Custom 
     - Record the conflict in explicit data structure and ask the user later. 
     - Replication tools provide conflict handler that gets called when conflict happens where we can write our own custom logic
     - Returninng all the conflicting values when user performs read and let them manually resolve it.

In real cases, it rarely makes sense for the multi-leader replication since it introduces more challenges along the way compared to single-leader. 

## Topologies :- 
![image](https://github.com/mystica2000/TIL/assets/45729256/1ba575da-80b5-4e82-a30c-bf54753e9363)

- Circular - the writes passes thru several leaders to reach all the replica
- Star - same as of circular
- Mesh (all to all) : Every Leader sends data to every other leader

Circular and Star : goes from one Node to another, and performing its own write and merges with new writes and passes it to the next node (double it and give it to the next person xD)
It might result in taking longer, what if in-between node goes down or having network issues, it will take more time than usual. 

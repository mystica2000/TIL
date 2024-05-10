# Leaderless Replication

- Instead of having a leader, there would be no centeralized leader for performing writes. Node can perform both read and write.
- Dynamo style
- Client sends write and read requests in parallel to several replicas.

## If there is no leader, how sync?!

- Instead of sending write to all other nodes and wait for acknowledgement, we can have like
- return write as successfull if write (w) is returned successfull on particular number of nodes and others writes async.
- Now, how do we know? all writes are successfull and have the same value across the nodes.

Writes donot have a "failover" like leadered replication does.

### Catchup: During Read,

- Read Repair - when read requests, send it to several nodes in parallel and compare the values, it might get different values if node was down when performing writes. To overcome that, compare the version number and repair all other nodes if version number didn't matched with latest.
- Anti-Entropy - Have a BG process that occationally checks if there is any data changes, if yes then copy the values from one to another. It doesnot copy in any particular order.

Ex: Riak, Voldemort

## Quorum

The number of writes (w) and number of reads (r) have to configured to follow w + r > n (number of replicas), smaller the value for w and r then more stale reads but provides high availability.
The w and r here are called quorum writes/reads.

![image](https://github.com/mystica2000/TIL/assets/45729256/149ecd22-d324-4975-a840-74a93c7511b1)


Note: n is not a fixed number of nodes that system has. system can contain more than n nodes too.

What if quorum conditions couldn't be satisfied for some reason (like network or maintence or some other issue)

- Can we return error that write/read is failed?
- Can we accept that atleast one of the replica is successfull? (This is called Sloppy quorum in which the system allows for the quorum condition to fail (ie) write was successfull for 2 nodes instead of designated 3 and continue operating - helps in having high availability and durability)

- Hinted Handoff; During Sloppy Quorum, some node accepts write on behalf of quorum designated write node, after that node comes online; The node which acted on behalf can give "hint" - catchup value for the node to be in sync with other nodes.

Leaderless replication is good for the cases where high availability, low latency and can withstand stale reads.

Reference: https://dataintensive.net/

# Transactional Logs

Database contains transactional logs which is write-ahead log (WAL) that performs writing db actions/events to the log before updating the database.

It is synchronous commit meaning that it writes to the log first before performing the transaction on the db. It ensures durability by waiting for the logs record to be written to stable storage (disk) before acknowleding the commit.

If db crashes while performing some actions, db can be restored by replaying the transactional log since the last checkpoint (also called rollforward)

This makes the db operations slightly slower since it performs write on the log and then update the db. Some DB offers to turn off WAL (but it might leads to data loss if crashed)

## Postgres

In Postgres, it is on by default - automatically enabled.
And Postgres offers another option called "Asynchronous commit" which allows transaction to complete more quickly by not waiting for the acknowledgement that is written to the disk but the tradeoff is that most recent transactions may lost if db crashed.
It introduces risk of data loss (not corruption since db will be replayed upto last record that was flushed and roll forwards the db to the self-consistent state).

Why most recent transactions not stored? If transaction A and B happens, B relies on transaction A then if we stored B but not A, data will be corrupt since A's effect is omited whereas B's effect is there but B relies on A.

## MySQL

In MySQL,

If storage engine is InnoDB, then transactional logs are enabled (called redo-logs here).
On the top of MySQL database, by default no logs are enabled. Binary Logs (binlog) are the behaves like redo-log irrespective of the storage engine and it can be turned on when starting a DB and it contains table creation/update and delete infos.

Both are not same but similar.

- redo-logs are storage engine level to ensure crash recovery and consistency meanwhile binlogs are at the server level records the sql statements executed on the server for auditing, replication, point-in-time recovery.

## Rollback vs Rollforward

Rollback refers to reverting DB to the previous state, un-doing the effects of uncommited transaction to maintain data integrity.
RollForward refers to restoring the DB by replaying the WAL and re-doing the missed out transactions to make the DB up-to-date and consistent.

Source:

https://www.postgresql.org/docs/current/wal-reliability.html
https://dev.mysql.com/doc/refman/5.7/en/binary-log.html

https://topic.alibabacloud.com/a/the-difference-between-mysql-innodbs-redo-log-and-binlog_1_41_30054519.html

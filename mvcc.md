# Multi-version Concurrency Control (MVCC) 
   - optimistic locking mechanism used in the DB storage engines (Mysql - InnoDB, Postgres)
   - lock don't block the user from accessing the data.

![mvcc](https://github.com/mystica2000/TIL/assets/45729256/26cefee6-0bb3-4b1d-9bd4-8fa68e1dad43)

From the diagram, 
  - current transaction can see their uncommited changes
  - other transactions see consistent snapshot of the data (which doesnot includes the transactions that is being performed by other transactions)
  - after COMMIT, the new transactions can able to see updated data.

From the name itself, we can see it has "multi-version" - while querying a database each transaction sees a snapshot of data (a database version) as it was some time ago, regardless of the current state of the underlying data. This protects the transaction from viewing inconsistent data that could be caused by (other) concurrent transaction updates on the same data rows, providing transaction isolation for each database session.


REFERENCES: https://www.postgresql.org/docs/7.1/mvcc.html

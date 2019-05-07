# Technical details for EOS History Indexer

cc32d9@gmail.com

# ScyllaDB schema

## Actions

For each action receipt, store a record as follows:

```
CREATE TABLE actions
 (
 block_num         BIGINT,
 block_time        TIMESTAMP,
 trx_id            ASCII,
 global_action_seq BIGINT,
 parent            BIGINT,
 contract          ASCII,
 action_name       ASCII,
 receiver          ASCII,
 PRIMARY KEY (block_num, global_action_seq, receiver)
 );


CREATE INDEX actions_trx_id ON actions (trx_id);

CREATE MATERIALIZED VIEW receiver_bloks AS
    SELECT receiver, block_num, block_time, trx_id, global_action_seq, parent, contract, action_name
    FROM actions
    WHERE receiver IS NOT NULL AND global_action_seq IS NOT NULL AND block_num IS NOT NULL
    PRIMARY KEY (receiver, block_num, global_action_seq)
    WITH CLUSTERING ORDER BY (block_num DESC);

```

## JSON traces

The database keeps fill JSON traces of all transactions for N days, and deletes old records:

```
CREATE TABLE traces
 (
 block_num         BIGINT,
 trx_id            ASCII,
 jsdata            BLOB,
 PRIMARY KEY (block_num, trx_id)
 );
```


## Transaction signatures

Block messages contain publuc keys that were signing each transaction. A
table will keep the signing keys for every transaction.

Need to optimize block messagge in Chronicle export to exclude the
encoded transaction bytestream, to reduce the traffic and CPU usage.


## RAM purchasing history

Tables keeping the history of RAM purchasing (also who bought for whom) and sales.


## Resource delegation history

Table that keeps the history of delegatebw/undelegatebw actions with
actors and target accounts.


## Token transfers and balances history

Two tables, one recording "transfer" and "issue" events for everything
that looks like a token, and the other one keeping track of balance
changes for everything that looks like a token balance.


## Account permissions

Two tables for actual mermissions: one keeps public keys assignment to
accounts, and the other one for recursive permissions.



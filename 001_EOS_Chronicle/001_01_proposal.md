# Work proposal: EOS History database

cc32d9@gmail.com


## Introduction

About the author: I am a senior engineer, with over 20 years of
experience in software and network engineering. I'm quite active in
open-source software development under my real name, and all public
activity related to EOS is done under a nickname `cc32d9`, which is the
beginning of sha256 from my real name. Investors of this project will
know me by real name. I am owning a consultancy company in Europe, and
all payments will be done with proper accounting and taxation.

As of today, there is no reliable and scalable solution for account
history in EOS blockchain.

The `history_plugin` that is part of the `eosio` software suite is not
reliable and not scalable, so maintaining it is already a hard job after
only 5 months of EOS operation. It keeps all account history in
chainbase database within the `nodeos` shared memory, which makes it
hard to maintain. Also there's no way of truncating the old data.

The `mongodb_plugin` has proven unreliable. Also it does not handle the
blockchain forks correctly.

I built my own ZMQ plugin for `nodeos`, and it exports all action
traces, alongside account balances and resources, via a ZeroMQ PUSH
socket. There is a prototype receiver written in Perl that stores the
history in a MySQL database. It's doing quite alright for certain
purposes, such as extracting specific reports for accounting or
statistical analysis (for example, EOSBet Dice fairness report, or some
specific snapshots for airdrops).

But the MySQL storage is not scalable enough for a public service where
thousands of users would query the account history. Also the ZeroMQ
socket may lose some messages in case of sender's or receiver's
shutdown.



## Scope of the work proposal

This work proposal aims to deliver free and open-source tools and
scripts, accompanied with detailed installation instructions.

Building a production service infrastructure is not covered in this
proposal.



## Functional requirements

The new history database should deliver quick results for the following
types of queries:

* ID of the latest action for an account. This could be a sequential
  integer or transaction hash, but it should be a quick and cheap way
  for the client to determine if there were any updates in account
  history.

* N latest actions in account history.

* First action in accout history.

* N sequential actions starting from a given sequence number. Subsequent
  requests are used in order to retrieve the whole history for an
  account.

* In the above action results, return either a brief result, or a full
  action trace in JSON format.

* For a given account, return all known token balances, based on
  "transfer" or "issue" or "open" actions.

* Retrieve a history of token transfers for an account. The requests
  should be paginated. Optional filters should include the currency
  and/or minimum amount (in order to filter out transfers of 0.0001
  EOS).

* Retrieve all current public keys for an account; Retrieve all accounts
  linked to a public key.

* For a given currency, retrieve the number of accounts and sum of all
  balances (and possibly other information, such as average balance or
  95th percentile of balances).

* (probably) retrieve a history of RAM allocations for an account.

* Producer voting statistics: detailed voting history per voter, per BP,
  and per proxy, as well as summary reports (top voter lists etc.).

* Name bidding history and current status (active and expired bids).

* RAM allocation statistics.

* (needs researching) Retrieve pending transactions for an account.

In addition, the database API should allow subscriptions to certain
events, such as all actions for a given account. It should also be
possible to subscribe retroactively, so that the database pushes the
history from a certain moment in time. The pushing mechanisms need to be
clarified. They should be lightweight and scalable.

The database should allow several concurrent sources (`nodeos`
processes) feeding the data. If possible, these sources should also be
geographically distributed.

The database should allow low-cost and automatic replication.

If possible, the front-end API should be able to retrieve the data from
the nearest server, and fall back to a different server if the nearest
one is not up to date.

A further research may suggest a P2P network for delivering the action
history to remote servers.

Ideally the API service should perform local caching and allow
client-side caching, with cheap and fast notifications about new
content.


## Potential solution

ScyllaDB is a re-implementation of Apache Cassandra in C++. It promises
extremely fast read-write operations and out-of-the-box replication. It
is very likely that the future solution will use ScyllaDB as the primary
storage for all blockchain and accounts history.

Apache Kafka is a powerful and flexible message queue, and it allows
storing the whole volume of events on a filesystem, allowing the
consumers read from any position in the past. Very likely it would also
prevent from loss of messages during the service shutdown or restart.

Once the storage and data delivery is resolved, a number of APIs and web
services need to be built in order to allow external users retrieve the
data in an efficient way. 

I have also made tests with RabbitMQ, and it turned out too slow for
EOS: the queue reader receives the events from the queue too slowly, and
RabbitMQ becomes a serious bottleneck. A re-synching `nodeos` makes the
queue grow unlimited, and a full re-sync would take months.


## Cost estimation

The cost estimation is based on the rate of 30 EOS per hour. This is
about my usual rate for engineering and development work. If it's
possible to outsource parts of the work, the total bill may probably be
reduced.

### RnD: 25 hours

A proof of concept needs to be built for Apache Kafka and ScyllaDB,
verifying their performance and scalability. If one of the components
turns out unsuitable, a replacement needs to be searched.

### nodeos plugins: 30 hours

The ZMQ plugin needs to be split in two parts: the data producer which
creates JSON messages, and the transport plugin which pushes the
messages by one of possible methods (such as ZMQ or Apache Kafka, or
probably directly into ScyllaDB).

Also the new plugin should keep the latest processed block number in
chainbase, in order to handle the forks more reliably after `nodeos`
restart.

Block One is developing a new `state_history_pugin`, and it will likely
be added as a complimentary or alternative source of data flow. This
needs to be investigated as part of RnD work.

### ScyllaDB database schema and testing: 40 hours

ScyllaDB (as well as Cassandra) has an SQL-like syntax, but the tables
should be designed differently than in RDBMS. Also ScyllaDB does not
support table joins, so the database schema will likely have data
duplication across several different tables for the sake of retrieval
speed.

Also the database should be designed for efficient replication, high
availability and geographical distribution.

### End-to-end testing and full re-sync: 40 hours

The whole constellation has to be tested for all possible edge cases,
such as handling the forks, performing a full re-sync, stopping or
crashing the services, and so on.


### Reference API: 40 hours

The reference API should cover the most demanded operations (account
history, token balances, and transfers history). The primary focus
should be done on developing the most flexible API standard, allowing
future extensions.

Other projects can later propose different implementations, but the goal
is to have one API standard that users can rely upon.


### Hosting costs

Hosting of physical and virtual servers needed for development and
testing is estimated as US$2000, or 360 EOS. Once the development is
complete, these servers would either be scrapped, or turned into
production systems if a follow-up project decides so.


### Total

Total work estimates as 175 hours, or 5250 EOS. With hosting costs, the
whole budget is estimated as 5610 EOS.



## Delivery and payments

The deatline for fixing the list of sponsors is Monday, 29 October 2018
00:00 UTC: https://bit.ly/2D2eP1c . Up to now, 6 sponsors have confirmed
and 2 more are in discussions.

The total budget will be equally split among the sponsors, and they will
place the deposits onto a multi-signature account that requires at least
3 signatures to initiate a transfer. Only the sponsors would have the
power of signing.

At the end of each stage I shall initiate an invoice with a detailed
time sheet, and continue working on the next stage after the payment is
made.

All the source code, test results, time sheets, and hosting costs will
be published on GitHub in public repositories.

Estimated end of the project is 3 months after the kick-off and budget
approval.

EOS account for payments: `cc32dninexxx`.



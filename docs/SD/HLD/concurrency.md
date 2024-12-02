---
title: Concurrency
summary: Concurrency in HLD
authors:
    - Maneesh Gupta
    - Nalluru Srinithya
date: 2024-12-02
# some_url: https://example.com
icon: fontawesome/solid/check
hide:
  - navigation
  - toc

tags:
  - hld
  - concurrency
---
What is DB Locking?

DB locking help us to make sure that not other transaction update the locked rows.

| Lock Type | Another Shared Lock | Another Exclusive Lock |
| :---: | --- | --- |
| Have Shared Lock | Yes | No |
| Have Exclusive Lock | No | No |

What are the isolation level present?

| Isolation level | Dirty Read Possible | Non Repeatable Read Possible  | Phantom Read Possible |
| --- | --- | --- | --- |
| Read Uncommitted | Yes | Yes | Yes |
| Read Committed | No  | Yes | Yes |
| Repeatable Read | No  | No  | Yes |
| Serializable | No | No | No |

Concurrency increases from bottom to top.

Consistency increases from top to bottom.

| Isolation Level | Locking Strategy |
| :--- | --- |
| Read Uncommitted | Read : No lock acquired Write: No lock acquired |
| Read Committed | Read: Shared lock acquired and released as soon as read is done. Write: Exclusive lock acquired and keep till the end of the transaction. |
| Repeatable Read | Read: Shared lock is acquired and released only at the end of the transaction. Write: Exclusive lock is acquired and released only at the end of the transaction. |
| Serializable | Same as repeatable read locking strategy + apply range lock and lock is release only at the end of the transaction.  |

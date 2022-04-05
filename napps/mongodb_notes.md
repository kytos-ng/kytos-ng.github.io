---
title: MongoDB Notes
parent: NApps
---

MongoDB Notes
=============

## Overview

This document provides helpful links and information about MongoDB.

## Links

### General information

- [Data modeling manual](https://www.mongodb.com/docs/manual/data-modeling/)
- [MongoDB schema design best practices](https://www.mongodb.com/developer/article/mongodb-schema-design-best-practices/)
- [Schema design anti patterns](https://www.mongodb.com/developer/article/schema-design-anti-pattern-massive-arrays/)
- [FAQ concurrency](https://www.mongodb.com/docs/manual/faq/concurrency/)

### Quick reference

- [Query and projection operators](https://www.mongodb.com/docs/manual/reference/operator/query/)
- [Expression operators](https://www.mongodb.com/docs/manual/meta/aggregation-quick-reference/#index-of-expression-operators)

### Replica set and clustering

- [Replica set elections](https://www.mongodb.com/docs/manual/core/replica-set-elections/)
- [Replica set write concern](https://www.mongodb.com/docs/manual/core/replica-set-write-concern/)
- [Replica set read concern](https://www.mongodb.com/docs/manual/reference/read-concern/)

### Courses

MongoDB University has some great free watch on-demand courses, if you're interested:

- [M320: Data Modeling](https://university.mongodb.com/courses/M320/about)
- [M121: MongoDB Aggregation Framework](https://university.mongodb.com/courses/M121/about)

### Profiling

- [explain](https://www.mongodb.com/docs/manual/tutorial/analyze-query-plan/)
- [db.setProfilingLevel](https://www.mongodb.com/docs/manual/reference/method/db.setProfilingLevel/)

### Transactions

- [In-progress Transactions and Write Conflicts](https://www.mongodb.com/docs/manual/core/transactions-production-consideration/?_ga=2.121978315.1116834295.1647796783-632507725.1644007955#in-progress-transactions-and-write-conflicts)

### Production notes

- [Recommended file systems](https://www.mongodb.com/docs/manual/administration/production-notes/#kernel-and-file-systems)
- [Back Up and Restore with MongoDB Tools](https://www.mongodb.com/docs/manual/tutorial/backup-and-restore-tools/)
- [Allocate Sufficient RAM and CPU](https://www.mongodb.com/docs/manual/administration/production-notes/#std-label-prod-notes-ram)

Also, it's desirable that the network connectivity from `kytosd` to MongoDB should be as reliable and as low latency as possible.

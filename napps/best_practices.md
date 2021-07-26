---
title: Best practices
parent: NApps
---
Best practices for Kytos NApp development
=========================================

Use of storehouse
-----------------

When using the filesystem backend there is no
guarantee that the order two write operations
are called will be the order they are executed.
To ensure that, it is advised the use of thread
locks, so one write operation is called only after
the previous one returns.

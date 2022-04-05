---
title: MongoDB
parent: NApps
---

MongoDB
=======

## Overview

MongoDB has been chosen as a default document-oriented database for NApps that need this type of storage. This document will highlight the current integration architecture and some points that NApp developers should be aware of when interfacing with MongoDB.


## Architecture

This high-level diagram illustrates how NApps are interfacing with MongoDB. `kytosd` will provide a thread safe client from `pymongo` connecting to a database instance named `napps` by default. NApps will write and read from this database and the connection pool can be configured accordingly, each NApp will have its collection. To avoid single points of failure, MongoDB will be deployed as a 3-node replica set cluster. In the future, the number of MongoDB nodes might be increased.


```
+-------------+              +--------------+
|             |              |              |
|   kytosd    |              |   MongoDB    |
|             |              | replica set  |
|  +-------+  |              |              |
|  |       |  |              |              |
|  | NApp1 +--+-------------->   NApp1's    |
|  +-------+  |              |(collections) |
|             |              |              |
|             |              |    napps     |
|  +-------+  |              |    (db)      |
|  |       |  |              |              |
|  | NAppN +--+-------------->   NAppN's    |
|  +-------+  |              |(collections) |
|             |              |              |
|             |              |              |
+-------------+              +--------------+
```

If MongoDB settings is set on `kytosd`, then it'll make sure to try to connect to it first before trying to initialize any NApps, this should facilitate bootstrapping certain operations such as creating indexes or any other operation at a NApp's `setup` time.

MongoDB version 5.0+ replica set provides a [write concern majority](https://www.mongodb.com/docs/manual/core/replica-set-write-concern/) by default, which means that the driver will write to the primary node, wait for it to replicate to the majority of secondary nodes and then return the response. The read concern on kytos will be primary preferred.


## NApps Modules

NApps are encouraged to stick with a three layer architecture (presentation, logic, data models):

```
         Presentation                         Logic                        Data Models
+-----------------------------+   +-----------------------------+   +----------------------+
|                             |   |                             |   |                      |
|   API & Event Handlers      |<->|  Controllers & Managers     |<->|   Database Models    |
|                             |   |        API Models           |   |                      |
+-----------------------------+   +-----------------------------+   +----------------------+
```


- **API & Events Handlers**: Primarily responsible for exposing endpoints and subscribing to events, database data access should be via controllers. API models schema initial validation is done at this layer.
- **Controllers & Business Managers**: Responsible for business logic, database CRUD encapsulation and operations, mapping API models (if API models are different than database models), interfacing between presentation and database models. A single controller will typically manage a single collection (or multiple if they are closely related). Managers can emerge to encapsulate more business logic, publish events or other operations that are not database related, they shouldn't write directly to the database but can map and modify for the controller to write if needed.
- **Database Models**: It's mainly responsible for defining [pydantic](https://pydantic-docs.helpmanual.io/) database schema models. Although MongoDB is schemaless, these database models provide base document structures just so database relations and operations can be modeled accordingly and easier to evolve and reason about. Also they provide certain validations without a round trip to the database.

### Code Sample

To illustrate a practical example, `upsert_switch` is a method of a controller that's responsible for upserting (update or insert) a switch document into a collection `switches`, notice that `SwitchDoc` is a `pydantic` database model that represents a database document, this method ensures that a `switch_dict` payload that will be parsed accordingly. In this example, the response isn't being used by an endpoint or another client, so it's not mapping to an API model (both models can be the same when applicable):

```
class SwitchDoc(BaseModel):
    """Switch DB Document Model."""

    id: str = Field(None, alias="_id")
    enabled: bool
    active: bool
    data_path: Optional[str]
    hardware: Optional[str]
    manufacturer: Optional[str]
    software: Optional[str]
    connection: Optional[str]
    ofp_version: Optional[str]
    serial: Optional[str]
    metadata: dict = {}
    interfaces: List[InterfaceSubDoc] = []
    inserted_at: Optional[datetime]
    updated_at: Optional[datetime]

class TopoController:

    def upsert_switch(self, dpid: str, switch_dict: dict) -> Optional[dict]:
        """Update or insert switch."""
        utc_now = datetime.utcnow()
        model = SwitchDoc(**{**switch_dict, **{"_id": dpid, "updated_at": utc_now}})
        updated = self.db.switches.find_one_and_update(
            {"_id": dpid},
            {
                "$set": model.dict(exclude={"inserted_at"}),
                "$setOnInsert": {"inserted_at": utc_now},
            },
            return_document=ReturnDocument.AFTER,
            upsert=True,
        )
        return updated
```

The following Python package and module structure is encouraged, that way NApps that are using this architecture it's evident where things are and how they are generally supposed to be evolved, facilitating for new developers too. On each package, you can start with a `__init__.py` module, as illustrated below and put the classes there, and if necessary you can create new modules under a specific package and move them. `api/models` don't necessarily need to be `pydantic` ones, you can also leverage `MongoDB` projections or use existing models that have already been implemented and tested:

```
db/models/__init__.py
db/models/some_model.py

controllers/__init__.py 
controllers/some_controller.py 

managers/__init__.py
managers/some_manager.py

api/models/__init__.py
api/models/some_model.py
```

## NApps write and read scenarios

These are the main write and read scenarios that a NApp will typically have:

- write/read when handling a request.
- write/read when handling an event.

NApps are responsible for handling database operations failure and retries accordingly, if a NApp is handling a request or an event where the caller is capable of handling an event error, then an error should be returned or sent. For some events where a caller isn't prepared to handle an error, this will typically be the case for certain network events such as PortStatus or similar events, then they should end up in a dead letter queue structure to eventually be re-injected or dealt with.

### Retries

`pymongo` official driver can handle one retry for read or write, however for handling more extreme cases the NApp should use [`tenacity`](https://github.com/jd/tenacity) library accordingly and adapt the type of retry that is suitable for a given operation.

## MongoDB Transactions

MongoDB supports [ACID updates for individual documents](https://www.mongodb.com/basics/acid-transactions). For multi-document updates, if certain types of operation orders and guarantees are needed then transactions can provide ACID properties too. However, as mentioned on [in-progress transactions and write conflicts](https://www.mongodb.com/docs/manual/core/transactions-production-consideration/?_ga=2.121978315.1116834295.1647796783-632507725.1644007955#in-progress-transactions-and-write-conflicts), writes conflicts can happen, so if single-document and multi-document concurrent updates are being handled with transactions, then threading lock is needed. Sometimes it's possible to avoid a transaction by modelling the problem in an embedded document, also, if you don't need to ensure the order of the operations, then it should be OK to not have a transaction and let the database handle the concurrent writes as they come. Retries and dead queue can also help to solve conflicts writes, but it's desirable to try to avoid them in the first place, so make sure to take this into account when modeling database models.

## Questions

### Where can I learn more or find more general information?

Check out <a href="/napps/mongodb_notes.html">MongoDB notes</a>.

### Why not an ORM?

- 1) `pymongo` has demonstrated to be sufficient for our needs, it abstracts connection pools and threads, but still exposes lower level constructs and allows you to use locks and deal with concurrency issues, which is crucial to have this kind of control considering how much concurrency is involved on Kytos-ng and NApps.
- 2) `pymongo` is very lightweight and the official driver, it's really great that it pretty much has one-to-one mapping to every operation that you'll find in the documentation, so as long as you find information in the docs, learning and finding solutions how to do it with `pymongo` should be very similar, unlike ORMs that sometimes abstract too much or force an entire different interface, and you'd have to figure out if it's supported or read their source code to see how it's been implemented. So far, in 2022, there's no SQLAlchemy-like library out there that doesn't abstract too much while empowering to solve problems easily and understanding how the database operations will be translated.
- 3) Some ORMs have `asyncio` support compatible drivers, but sometimes still over abstract, so composing with kytos `asyncio` core would be challenging to evolve, unlike [motor](https://github.com/mongodb/motor) an official MongoDB async driver that has been designed to compose well with `pymongo` and `asyncio`.

### Why `pydantic` for database models?

- `pydantic` is stable, battle-tested, and it's great at parsing schemas while ensuring validations too. It's also aligned with potential usage to parse and validate events when the existing validations don't cover yet.

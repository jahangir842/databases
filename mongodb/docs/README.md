# **MongoDB**

### *(Designed for DevOps/System Admin + Cloud Engineer profiles)*

---

# **1. Introduction to MongoDB**

MongoDB is a **NoSQL, document-oriented database** that stores data as **BSON** (Binary JSON).
Each record is called a **document**, stored inside a **collection**.

### **Key Characteristics**

* Schema-flexible
* Horizontally scalable
* High availability via replica sets
* High performance for read/write
* JSON-like storage structure

### **Simple Example Document**

```json
{
  "name": "Jagz",
  "role": "DevOps Engineer",
  "skills": ["Python", "Linux", "Azure"]
}
```

### **When to Use MongoDB**

* When data structure changes often
* When you need fast writes
* For analytics with dynamic schema
* For storing logs, IoT, user profiles, catalogs

### **Memorization Trick**

**Remember: M-O-N-G-O → “Mostly Organized, Not rigid, Grows Outward”**
Meaning: MongoDB is mostly structured, schema-flexible, and horizontally scalable.

---

# **2. MongoDB Architecture**

MongoDB has 3 major components:

### **1. mongod**

Database server process
Responsible for data storage, replication, sharding.

### **2. mongos**

Query router (used only in sharded environments)

### **3. Mongo Shell / Drivers**

Client interface to run operations.

---

# **3. MongoDB Data Model**

### **Hierarchy**

`Database → Collections → Documents → Fields`

### **Documents = JSON-like structures**

Documents can be nested, unlike SQL rows.

### **Example**

```json
{
  "user_id": 123,
  "orders": [
    { "id": 1, "item": "Laptop" },
    { "id": 2, "item": "Phone" }
  ]
}
```

### **Memorization Trick**

**SQL Rows → Mongo Documents**
**SQL Tables → Mongo Collections**

---

# **4. Replication – MongoDB Replica Set**

A **replica set = primary + secondaries + arbiter (optional)**

### **Why replication?**

* High availability
* Automatic failover
* Redundancy
* Read scaling

### **Replica Set Example**

* **Primary**: receives all writes
* **Secondary 1**: replicates data
* **Secondary 2**: can serve reads
* **Arbiter**: only votes, doesn’t store data

### **Failover process**

* If primary goes down → secondaries elect a new primary
* Election typically finishes within *2–12 seconds*

### **Memorization Trick**

Think of **P-S-A** → **Primary, Secondary, Arbiter**
(Primary writes, Secondary copies, Arbiter votes.)

---

# **5. Read Concerns**

Read concern defines **consistency** level for read operations.

### **Levels**

| Level          | Meaning                                    | Example Use       |
| -------------- | ------------------------------------------ | ----------------- |
| `local`        | Returns data from local node, may be stale | Fast reads        |
| `available`    | Useful for sharded clusters                | Geo-distr. reads  |
| `majority`     | Only returns data acknowledged by majority | Consistent reads  |
| `linearizable` | Strongest; guarantees global order         | Financial apps    |
| `snapshot`     | For multi-doc transactions                 | Banking workflows |

### **Example Command**

```javascript
db.users.find().readConcern("majority")
```

### **Memorization Trick**

Think: **L-A-M-L-S → “Local Apps May Lack Sync”**
(These are the 5 levels.)

---

# **6. Write Concerns**

Write concern controls **acknowledgment** level for writes.

### **Levels**

| Level        | Meaning                       |
| ------------ | ----------------------------- |
| `w:0`        | Fire and forget               |
| `w:1`        | Primary acknowledged          |
| `w:majority` | Majority of nodes acknowledge |
| `w:n`        | n nodes must acknowledge      |
| `j:true`     | Ensure write is journaled     |

### **Example Write Concern**

```javascript
db.orders.insertOne(
  { item: "Laptop", qty: 1 },
  { writeConcern: { w: "majority", j: true } }
)
```

### **Memorization Trick**

**Write Concerns → W.J (Write + Journal)**
Remember: higher W = safer but slower.

---

# **7. MongoDB Sharding (Distribution / Horizontal Scaling)**

When a single node cannot store all data or handle all traffic, sharding is used.

### **Why Sharding?**

* Handle very large datasets
* Distribute load
* Achieve horizontal scale
* Reduce hotspotting

### **Components**

1. **Shard Server (mongod)** – stores subset of data
2. **Config Servers (3 nodes)** – store cluster metadata
3. **mongos router** – routes queries to correct shard

### **Shard Key**

Determines how data is distributed.
Must be:

* High cardinality
* Evenly distributed
* Immutable (ideally)

### **Shard Key Example**

```javascript
sh.shardCollection("ecommerce.orders", { userId: 1 })
```

### **Sharding Strategies**

| Strategy          | Uses                             |
| ----------------- | -------------------------------- |
| **Range-based**   | Easy sorting, may cause hotspots |
| **Hashed**        | Great distribution, no hotspots  |
| **Zone sharding** | Geo data, regulatory compliance  |

### **Memorization Trick**

**“SHARD” → Split Huge Amounts into Regions/Divisions**

---

# **8. Indexing in MongoDB**

Indexes speed up reads.

### **Types**

* Single field
* Compound
* Multikey (arrays)
* Text index
* Geospatial index
* TTL index (auto-delete docs)

### **Example**

```javascript
db.users.createIndex({ "email": 1 }, { unique: true })
```

### **Indexing Rule**

Indexes = like book index → fast lookup

---

# **9. MongoDB CRUD Operations**

### **Insert**

```javascript
db.users.insertOne({name: "Jagz"})
```

### **Find**

```javascript
db.users.find({role: "DevOps"})
```

### **Update**

```javascript
db.users.updateOne({name: "Jagz"}, {$set: {role: "Cloud Engineer"}})
```

### **Delete**

```javascript
db.users.deleteOne({name: "Jagz"})
```

---

# **10. Aggregation Framework**

Used for analytics and transformations (similar to SQL GROUP BY).

### **Example**

```javascript
db.orders.aggregate([
  { $match: { status: "completed" }},
  { $group: { _id: "$userId", total: { $sum: "$amount" }}}
])
```

---

# **11. Transactions (Multi-Document)**

MongoDB supports ACID transactions since v4.0.

### **Usage Example**

```javascript
const session = db.getMongo().startSession();
session.startTransaction();

db.accounts.updateOne({user: "A"}, {$inc: {balance: -100}}, {session});
db.accounts.updateOne({user: "B"}, {$inc: {balance: 100}}, {session});

session.commitTransaction();
session.endSession();
```

### **Note**

Use transactions only when necessary—they slow down performance.

---

# **12. Storage Engine – WiredTiger**

WiredTiger is the default storage engine.

### **Features**

* Document-level locking
* Compression (snappy/zlib)
* Journaling
* Better concurrency

---

# **13. MongoDB Deployment Options**

### **On-Premises**

* Self-hosted on Linux/Windows
* Replica sets + sharding
* Backup using mongodump, ops manager, or filesystem snapshots

### **Cloud**

* **MongoDB Atlas (official)**

  * Fully managed cluster
  * Auto scaling, backups
  * Multi-cloud

### **Cloud Alternatives Comparison**

| Task                       | AWS                  | Azure                | Huawei Cloud | Open Source           |
| -------------------------- | -------------------- | -------------------- | ------------ | --------------------- |
| Mongo-compatible service   | DocumentDB           | CosmosDB (Mongo API) | DDS          | Self-hosted + Percona |
| Real Mongo?                | ❌ (compatible only)  | ❌ (API only)         | ✔            | ✔                     |
| Best option for pure Mongo | Atlas or self-hosted |                      |              |                       |

---

# **14. Backup and Restore**

### **Backup Tools**

* `mongodump / mongorestore` (logical)
* Ops Manager / Atlas (automated)
* EBS snapshots (block-level)

### **Example**

```bash
mongodump --db=mydb --out=/backup/
mongorestore /backup/
```

---

# **15. Common Interview Questions**

### **1. Difference between MongoDB & SQL?**

* Schema-less
* Documents instead of rows
* Horizontal scaling
* High write efficiency

### **2. What is a replica set?**

Group of mongod instances with auto failover.

### **3. What is sharding?**

Distributing data across nodes for scale-out.

### **4. How does MongoDB ensure consistency?**

Through write concerns + read concerns + replication.

### **5. What is WiredTiger?**

Default storage engine providing performance + compression.

---

# **16. Memorization Summary**

* **Replica Set = P-S-A (Primary, Secondary, Arbiter)**
* **Read Concerns = L-A-M-L-S (“Local Apps May Lack Sync”)**
* **Sharding = Split Huge Amounts into Regions/Divisions**
* **MongoDB = Mostly Organized, Not rigid, Grows Outward**

---

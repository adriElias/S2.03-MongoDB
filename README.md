# S203-MongoDB: Optical Store Database Design

**Project**: MongoDB database design exercises for an optical store ("Cul d'Ampolla") with three different data modeling approaches and perspectives.

---

## 🎯 Project Overview

This project explores MongoDB database design through a real-world optical store scenario. It includes three mini-projects that demonstrate different ways to model and query the same data depending on the primary use case.

**Scenario**: An optical store wants to digitize client/employee management, glasses inventory, sales tracking, and supplier relationships.

---

## 📚 Three Mini-Projects

### 1️⃣ **base_optical_store** - Base Implementation
**Focus**: General-purpose database design  
**Use Case**: Complete system overview with balanced normalization

- Comprehensive schema with all entities: clients, employees, glasses, providers, sales
- Standard normalization with `ObjectId` references between collections
- Flexible for multiple query patterns
- Foundation for the other two specialized implementations

**Files**: 
- Schema: `mongoDB/optical_store.json`
- Data: `schema/optical_store/` (BSON dumps)

---

### 2️⃣ **n1e1-opticalStore** - Customer View (Exercise 1)
**Focus**: Customer portal optimization  
**Use Case**: Customer-centric queries and interactions

- Database design optimized for **customer-facing operations**
- Efficient profile management, purchase history, glasses search
- Customer self-reference for referral tracking
- Best for: E-commerce portals, customer dashboards

**Key Features**:
- Quick profile & referrer lookup
- Complete purchase history with related data
- Glasses search with filters (brand, price, frame type)
- Customer recommendations

**Schema**: `mongoDB/customer_view.json`

---

### 3️⃣ **n1e2-opticalStore** - Glasses View (Exercise 2)
**Focus**: Inventory & catalog management  
**Use Case**: Glasses-centric operations and analytics

- Database design optimized for **inventory & analytics**
- Denormalized structure: embedded provider info & buyer arrays in glasses documents
- Fast catalog queries without multiple joins
- Best for: Inventory systems, sales analytics, provider performance reports

**Key Features**:
- Fast glasses catalog search with embedded provider details
- Buyer tracking array for sales analytics
- Provider relationship management
- Sales performance metrics

**Schema**: `mongoDB/glasses_view.json`  
**Database**: `optical_store_2` (separate instance)

---

## 📊 Comparison Table

| Aspect | Base | N1E1 (Customer) | N1E2 (Glasses) |
|--------|------|-----------------|----------------|
| **Design Goal** | General balance | Customer portal | Inventory system |
| **Normalization** | Standard | Minimal | Denormalized |
| **Primary Entity** | Multi-purpose | Client | Glasses |
| **Query Pattern** | Flexible | By customer_id | By glasses_id |
| **Optimization** | Balanced | Profile & purchases | Catalog & analytics |
| **Embedded Data** | None | Minimal | Provider & buyers |

---

## 🛠 Technologies

- **Database**: MongoDB (NoSQL)
- **Containerization**: Docker, Docker Compose
- **Validation**: JSON Schema
- **Export**: mongodump (BSON format)
- **CLI**: mongosh for database queries

---

## 📁 Project Structure

```
S203-MongoDB/
├── .env                            # Environment configuration (gitignored)
├── .env.example                    # Template for environment variables
├── .gitignore                      # Git ignore rules
├── docker-compose.yml              # Docker setup
├── README.md                       # This file
│
├── base_optical_store/             # Base implementation
│   ├── README.md
│   ├── mongoDB/
│   │   └── optical_store.json      # Complete schema
│   └── schema/
│       └── optical_store/          # BSON dumps
│
├── n1e1-opticalStore/              # Customer view
│   ├── README.md
│   ├── mongoDB/
│   │   └── customer_view.json
│   └── schema/
│       └── optical_store/
│
└── n1e2-opticalStore/              # Glasses view
    ├── README.md
    ├── mongoDB/
    │   └── glasses_view.json
    └── schema/
        └── optical_store_2/
```

---

## 🚀 Quick Start

### Setup

```bash
# Clone and navigate
cd S203-MongoDB

# Copy environment template
cp .env.example .env

# Edit .env with your MongoDB credentials
```

### Start MongoDB

```bash
# Start container
docker-compose up -d

# Verify it's running
docker ps | grep mongodb
```

### Import Data

```bash
# For base_optical_store & n1e1-opticalStore
mongorestore --uri="mongodb://admin:mi_password@localhost:27017/?authSource=admin" \
  --nsInclude="optical_store.*" ./optical_store_backup

# For n1e2-opticalStore
mongorestore --uri="mongodb://admin:mi_password@localhost:27017/?authSource=admin" \
  --nsInclude="optical_store_2.*" ./optical_store_backup
```

### Test Queries

```bash
mongosh "mongodb://admin:mi_password@localhost:27017/?authSource=admin"

# Base: View all collections
> use optical_store
> show collections

# N1E1: Customer queries
> db.clients.findOne()
> db.sales.find({ client_id: ObjectId("...") })

# N1E2: Glasses queries
> use optical_store_2
> db.glasses.findOne()
> db.glasses.find({ "price": { $lte: 200 } })
```

---

## 📖 Documentation

Each mini-project includes detailed README with:
- ✅ Exercise objectives
- ✅ Feature descriptions
- ✅ UX/UI diagrams
- ✅ ER diagrams
- ✅ Data structure examples
- ✅ Design rationale
- ✅ Quick start commands

**Start reading**:
- [base_optical_store/README.md](base_optical_store/README.md)
- [n1e1-opticalStore/README.md](n1e1-opticalStore/README.md)
- [n1e2-opticalStore/README.md](n1e2-opticalStore/README.md)

---

## 🧠 Learning Path

**Beginner** → Start with `base_optical_store`  
Understand complete database structure with standard normalization

**Intermediate** → Move to `n1e1-opticalStore`  
Learn how to optimize for specific use cases (customer portal)

**Advanced** → Explore `n1e2-opticalStore`  
Master denormalization and embedded documents for analytics

---

## 🔑 Key Concepts Demonstrated

- **Normalization vs Denormalization**: Base vs N1E2
- **Embedded Documents**: Provider/buyer snapshots in N1E2
- **Self-References**: Customer referral system in N1E1
- **Array Operations**: Buyer tracking in glasses documents
- **Aggregation Pipelines**: Complex multi-collection queries
- **Schema Validation**: JSON Schema enforcement at database level
- **Relationship Patterns**: ObjectId references and $lookup joins

---

## 💾 Database Credentials

```
Username: admin
Password: mi_password
Port: 27017
Databases: 
  - optical_store (base + n1e1)
  - optical_store_2 (n1e2)
```

> **Important**: Store credentials in `.env` file (gitignored for security)

---

## 📅 Timeline

- **Base**: Complete general schema with all collections
- **N1E1**: Customer-view optimization (Exercise 1)
- **N1E2**: Glasses-view with denormalization (Exercise 2)

---

**Last updated**: March 23, 2026  
**Course**: Database Design & MongoDB (Sprint 2)

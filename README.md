# S203-MongoDB: Optical Store Database Design

**Project**: MongoDB database design exercises for an optical store ("Cul d'Ampolla") with three different data modeling approaches and perspectives.

---

## 🎯 Project Overview

This project explores MongoDB database design through real-world scenarios with five mini-projects. Each demonstrates different ways to model and query data depending on the primary use case:

**Scenario 1 - Optical Store**: "Cul d'Ampolla" needs to digitize client/employee management, glasses inventory, sales tracking, and supplier relationships.

**Scenario 2 - Food Delivery**: Pizzeria platform requires order management, customer tracking, product catalogs, and delivery logistics.

---

## 📚 Five Mini-Projects

### 🏪 **OPTICAL STORE EXERCISES**

#### 1️⃣ **base_optical_store** - Base Implementation
**Focus**: General-purpose database design  
**Database**: Complete system with balanced normalization

- Comprehensive schema: clients, employees, glasses, providers, sales
- Standard normalization with `ObjectId` references
- JSON schema validation for data integrity
- Complete exercise statement with all requirements

**Best for**: Understanding full database structure and relationships

---

#### 2️⃣ **n1e1-opticalStore** - Customer View (Exercise 1)
**Focus**: Customer portal optimization  
**Database**: Customer-centric queries

- Optimized for **customer-facing operations**
- Efficient profile management, purchase history, glasses search
- Customer self-references for referral tracking
- UI diagram showing customer information display

**Best for**: E-commerce portals, customer dashboards

---

#### 3️⃣ **n1e2-opticalStore** - Glasses View (Exercise 2)
**Focus**: Inventory & catalog management  
**Database**: Denormalized for analytics

- Optimized for **inventory & analytics**
- Embedded provider info & buyer arrays in glasses documents
- Fast catalog queries without multiple joins
- UI diagram showing inventory search and buyer tracking

**Best for**: Inventory systems, sales analytics, provider performance

---

### 🍕 **FOOD DELIVERY EXERCISES**

#### 4️⃣ **n2e1-base_pizzeria** - Base Implementation
**Focus**: Complete platform database design  
**Database**: Full food delivery platform

- Complete schema: customers, orders, products, stores, employees, deliveries
- Dynamic pizza categories and product management
- Employee role distinction (cook vs delivery driver)
- Delivery tracking with driver assignment
- JSON schema validation at database level

**Best for**: Understanding multi-entity relationships in delivery platforms

---

#### 5️⃣ **n2e1-pizzeria** - Order Confirmation View (Exercise 1)
**Focus**: Order confirmation optimization  
**Database**: Order-centric with embedded snapshots

- Optimized for **order confirmation display**
- Embedded customer data and product line items
- Complete order details in single document
- Delivery information with special instructions
- UI diagram showing order confirmation page layout

**Best for**: Customer order tracking, order confirmation pages

---

## 📊 Comparison Table

### Optical Store Projects

| Aspect | Base | N1E1 (Customer) | N1E2 (Glasses) |
|--------|------|-----------------|----------------|
| **Design Goal** | General balance | Customer portal | Inventory system |
| **Normalization** | Standard | Minimal | Denormalized |
| **Primary Entity** | Multi-purpose | Client | Glasses |
| **Query Pattern** | Flexible | By customer_id | By glasses_id |
| **Optimization** | Balanced | Profile & purchases | Catalog & analytics |
| **Embedded Data** | None | Minimal | Provider & buyers |

### Food Delivery Projects

| Aspect | N2E1 Base | N2E1 (Order View) |
|--------|----------|------------------|
| **Design Goal** | Complete platform | Order confirmation |
| **Normalization** | Standard references | Denormalized snapshots |
| **Primary Entity** | Multi-entity | Order |
| **Query Pattern** | Complex joins | Single document lookup |
| **Optimization** | Flexible queries | Fast confirmation display |
| **Embedded Data** | None | Customer & products |

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
├── base_optical_store/             # Optical: Base implementation
│   ├── README.md
│   ├── mongoDB/
│   │   └── optical_store.json      # Complete schema
│   └── schema/
│       └── optical_store/          # BSON dumps
│
├── n1e1-opticalStore/              # Optical: Customer view
│   ├── README.md
│   ├── mongoDB/
│   │   └── customer_view.json
│   └── schema/
│       └── optical_store/
│
├── n1e2-opticalStore/              # Optical: Glasses view
│   ├── README.md
│   ├── mongoDB/
│   │   └── glasses_view.json
│   └── schema/
│       └── optical_store_2/
│
├── n2e1-base_pizzeria/             # Pizzeria: Base implementation
│   ├── README.md
│   ├── mongoDB/
│   │   └── pizzeria.json           # Complete schema
│   └── schema/
│       └── pizzeria/               # BSON dumps
│
└── n2e1-pizzeria/                  # Pizzeria: Order confirmation view
    ├── README.md
    ├── mongoDB/
    │   └── pizzeria.json
    └── schema/
        └── pizzeria/               # BSON dumps
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
# Load environment variables from .env
set -a
source .env
set +a

# For base_optical_store & n1e1-opticalStore
mongorestore --uri="mongodb://$MONGO_USER:$MONGO_PASSWORD@localhost:27017/?authSource=admin" \
  --nsInclude="optical_store.*" ./optical_store_backup

# For n1e2-opticalStore
mongorestore --uri="mongodb://$MONGO_USER:$MONGO_PASSWORD@localhost:27017/?authSource=admin" \
  --nsInclude="optical_store_2.*" ./optical_store_backup
```

### Test Queries

```bash
# Load environment variables from .env
set -a
source .env
set +a

mongosh "mongodb://$MONGO_USER:$MONGO_PASSWORD@localhost:27017/?authSource=admin"

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
- ✅ Exercise objectives and requirements
- ✅ Feature descriptions
- ✅ UX/UI diagrams
- ✅ ER diagrams showing relationships
- ✅ Data structure examples
- ✅ Design rationale tables
- ✅ Quick start commands

**Start reading**:

**Optical Store:**
- [base_optical_store/README.md](base_optical_store/README.md)
- [n1e1-opticalStore/README.md](n1e1-opticalStore/README.md)
- [n1e2-opticalStore/README.md](n1e2-opticalStore/README.md)

**Food Delivery:**
- [n2e1-base_pizzeria/README.md](n2e1-base_pizzeria/README.md)
- [n2e1-pizzeria/README.md](n2e1-pizzeria/README.md)

---

## 🧠 Learning Path

**Beginner** → Start with `base_optical_store` or `n2e1-base_pizzeria`  
Understand complete database structures with standard normalization

**Intermediate** → Move to `n1e1-opticalStore` or `n2e1-pizzeria`  
Learn how to optimize for specific use cases (customer view, order confirmation)

**Advanced** → Explore `n1e2-opticalStore`  
Master denormalization and embedded documents for analytics queries

---

## 🔑 Key Concepts Demonstrated

**Normalization & Denormalization:**
- Base implementations use standard normalization with ObjectId references
- Specialized views (N1E2, N2E1-pizzeria) use denormalization with embedded documents

**Data Modeling Patterns:**
- Embedded documents (provider info in glasses, customer data in orders)
- Array fields for tracking relationships (buyers array, product arrays)
- Self-references (customer referral system)
- External references with $lookup joins (base implementations)

**Query Optimization:**
- Document-level filtering for fast lookups
- Single-query retrieval with embedded data
- Aggregation pipelines for analytics
- Index strategies for common query patterns

**MongoDB Features:**
- JSON Schema validation at database level
- BSON dumps for backup and data portability
- mongosh CLI for testing and verification
- Docker containerization for consistent deployment

---

## 📅 Projects Timeline

### Phase 1: Optical Store Exercises
- **base_optical_store**: Complete schema with all entities
- **n1e1-opticalStore**: Customer-view optimization
- **n1e2-opticalStore**: Glasses-view with denormalization

### Phase 2: Food Delivery Exercises
- **n2e1-base_pizzeria**: Complete platform schema
- **n2e1-pizzeria**: Order confirmation view optimization

---

## 💾 Security & Credentials

All sensitive information (credentials, passwords) is stored in `.env` file which is **gitignored**.

**Template (.env.example):**
```bash
MONGO_USER=admin
MONGO_PASSWORD=your_secure_password
MONGO_PORT=27017
```

> **Important**: Never commit actual `.env` file with credentials. Always use environment variables in your code.

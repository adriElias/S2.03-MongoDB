# Optical Store "Cul d'Ampolla" - Glasses View (N1E2)

**Description**: MongoDB database design from the glasses/catalog perspective of the optical store. This exercise (N1E2) focuses on managing and querying the glasses catalog with embedded provider and buyer information for efficient denormalization.

**Exercise**: N1E2 - Database diagram and design decisions from glasses/inventory view.

---

## 📌 Overview

This exercise models a database optimized for a glasses inventory management system, showing how to structure data for efficient catalog operations: glasses availability, sales tracking, and provider relationships.

---

## 🎯 Key Features

- **Glasses Catalog** - Brand, price, provider details (embedded)
- **Buyer Tracking** - List of customers who purchased each glasses model
- **Provider Info** - Embedded provider snapshot for fast queries
- **Denormalized Structure** - Optimized for glasses-centric operations

---

## 🛠 Technologies

- **Database**: MongoDB (NoSQL)
- **Containerization**: Docker, Docker Compose
- **Validation**: JSON Schema

---

## 🧩 UX/UI Diagram - Glasses Management View

### 📊 Inventory Management Display

```
┌────────────────────────────────────────────────────────────────┐
│           OPTICAL STORE - GLASSES SEARCH & ANALYTICS           │
├────────────────────────────────────────────────────────────────┤
│                                                                │
│  ┌──────────────────────────────┐  ┌──────────────────────┐  │
│  │    SEARCH GLASSES            │  │    BOUGHT BY         │  │
│  ├──────────────────────────────┤  ├──────────────────────┤  │
│  │                              │  │                      │  │
│  │  Brand: [Brand________]      │  │  • Client 1          │  │
│  │  Frame: [Metallic ▼]         │  │  • Client 2          │  │
│  │  Provider: [Googles Ass...] 🔍  │  • Client 3          │  │
│  │  Price: [105.75-€]           │  │  • Client 4          │  │
│  │                              │  │  • Client 5          │  │
│  │        [Submit]              │  │  • ...               │  │
│  │                              │  │                      │  │
│  │  💡 🔍 Provider popup        │  │  💡 Click → Detail   │  │
│  │  shows detailed info         │  │  view per client     │  │
│  │                              │  │                      │  │
│  └──────────────────────────────┘  └──────────────────────┘  │
│                                                                │
│  ┌──────────────────────────────────────────────────────────┐ │
│  │ RESULTS: Google Glasses by Googles Associated SL         │ │
│  ├──────────────────────────────────────────────────────────┤ │
│  │ ✓ Brand: Google                                          │ │
│  │ ✓ Frame: Metallic (Gold)                                │ │
│  │ ✓ Price: 105.75€                                        │ │
│  │ ✓ Total Buyers: 8 customers                             │ │
│  │ ✓ Provider: Googles Associated SL (Madrid, Spain)       │ │
│  │ ✓ Provider Contact: +34-911-111-111 | FAX: 911-222-222 │ │
│  └──────────────────────────────────────────────────────────┘ │
│                                                                │
└────────────────────────────────────────────────────────────────┘
```

---

## 📐 ER Diagram from Glasses View

```
            ┌────────────────────┐
            │     GLASSES        │
            ├────────────────────┤
            │ _id                │
            │ brand              │
            │ price              │
            │ provider (embedded)│ ──┐
            │ buyers (array)     │ ──┼─ Embedded snapshots
            └────────────────────┘  │
                  ▲                  │
                  │                  │
        ┌─────────┴──────────┐       │
        │                    │       │
   ┌─────────┐          ┌─────────┐ │
   │PROVIDERS│          │ CUSTOMERS│ │
   ├─────────┤          ├──────────┤ │
   │ _id     │          │ _id      │ │
   │ name    │          │ name     │ │
   │address  │          │ email    │ │
   │contact  │          │ address  │ │
   └─────────┘          └──────────┘ │
        ▲                             │
        │ (referenced)          (denormalized
        └─────────────────────────────┘
            via provider_id & customer_id)
```

---

## 🔍 Use Cases & Data Structure

**Glasses Workflow:**
1. View glasses inventory with provider & buyer stats
2. Track sales history for each glasses model
3. Analyze provider performance
4. Update glasses availability & pricing

**Core Collections:**
- **glasses**: Brand, price, **provider** (embedded), **buyers** (array)
- **providers**: Name, address, contact, NIF
- **customers**: Name, email, address, registration_at, referred_by

**Key Denormalization:**
- Provider info embedded in glasses for fast queries
- Buyers array tracks customers without separate collection join

---

## 🎨 Design Rationale

| Decision | Rationale |
|----------|-----------|
| **Embedded Provider** | Fast access to provider info without joins |
| **Buyers Array** | Track customers per glasses model efficiently |
| **Denormalized Structure** | Optimized for catalog & analytics queries |
| **Snapshot Data** | Provider/buyer names cached for reporting |

---

## 🚀 Quick Start

```bash
# Start MongoDB
docker-compose up -d

# Import data
mongorestore --uri="mongodb://admin:mi_password@localhost:27017/?authSource=admin" \
  --nsInclude="optical_store_2.*" ./optical_store_backup

# Test queries
mongosh "mongodb://admin:mi_password@localhost:27017/?authSource=admin"
> use optical_store_2
> db.glasses.findOne()
> db.glasses.find({ "price": { $lte: 200 } }).limit(10)
> db.glasses.aggregate([
    { $unwind: "$buyers" },
    { $group: { _id: "$_id", buyer_count: { $sum: 1 } } }
  ])
```

---

## 📁 Project Structure (N1E2)

```
n1e2-opticalStore/
├── README.md                       # This file
├── doc/                            # Documentation
├── mongoDB/
│   └── glasses_view.json           # Schema from glasses view
└── schema/
    └── optical_store_2/
        ├── customer.bson
        ├── customer.metadata.json
        ├── glasses.bson
        ├── glasses.metadata.json
        ├── provider.bson
        ├── provider.metadata.json
        └── prelude.json
```

---

## 📝 Differences: N1E1 vs N1E2

| Aspect | N1E1 (Customer) | N1E2 (Glasses) |
|--------|-----------------|----------------|
| **Focus** | Customer portal | Inventory management |
| **Optimization** | Profile & purchases | Catalog & analytics |
| **Denormalization** | Minimal | Embedded provider/buyers |
| **Primary Query** | By customer_id | By glasses_id |
| **Schema** | `customer_view.json` | `glasses_view.json` |

---

**Last updated**: March 23, 2026
**Exercise**: N1E2 - Glasses/Inventory view with denormalized provider & buyer data

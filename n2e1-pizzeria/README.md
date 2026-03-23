# Food Delivery Platform - Order Confirmation View (N2E1)

**Description**: MongoDB database design optimized for order confirmation and tracking. This exercise (N2E1) focuses on displaying complete order details with customer information, delivery address, and itemized product list for customer portal.

**Exercise**: N2E1 - Order confirmation view with embedded data snapshots.

---

## 📌 Overview

This mini-project models a database optimized for displaying confirmed orders to customers. It shows order items, customer details, delivery information, and payment/delivery status tracking in a single query.

---

## 🎯 Key Features

- **Order Confirmation Display** - Complete order #ID with itemized products
- **Embedded Customer Data** - Name, address, contact info snapshot
- **Product Line Items** - Quantity, price, product details per item
- **Delivery Information** - Address with street, number, floor, door, notes
- **Order Status** - Delivery type (home/pickup) and tracking (Delivered/Paid)
- **Store Reference** - Which store manages the order
- **Contact Notes** - Special delivery instructions

---

## 🛠 Technologies

- **Database**: MongoDB (NoSQL)
- **Containerization**: Docker, Docker Compose
- **Validation**: JSON Schema
- **CLI**: mongosh for queries

---

## 🧩 UI/UX Diagram - Order Confirmation Page

### 📊 Order Confirmation Display

```
┌─────────────────────────────────────────────────────────────┐
│   Shop: Food for couch potatoes                             │
│   Confirmed Order #123                                      │
├──────────────────────────┬─────────────────────────────────┤
│ ORDER ITEMS              │ CUSTOMER & DELIVERY INFO         │
│                          │                                 │
│ 1x Pizza Margrita        │ Client                          │
│    (Thin dough)    8€    │ ID: 1234                        │
│ 2x Pizza BBQ             │ Name: Person, True              │
│    (Normal dough)  24€    │                                 │
│ 2x Cheese Hamburger      │ Deliver in                      │
│                  12€    │ Street: Right Street            │
│ 1x Double Hamburger      │ Number: 42b                     │
│               8€    │ Floor: 3 I                      │
│ 2x Water        4€    │                                 │
│ 2x Coke         5€    │ Contact Number: 6281321         │
│                          │                                 │
│ Total:             61€   │ Note: We don't have             │
│                          │ elevation! 📍                  │
├──────────────────────────┴─────────────────────────────────┤
│ [Delivered]                              [Paid]             │
└─────────────────────────────────────────────────────────────┘
```

---

## 📐 ER Diagram - Order View

```
        ┌──────────────────┐
        │      ORDERS      │
        ├──────────────────┤
        │ _id              │
        │ order_id         │
        │ order_date       │
        │ delivery_type    │
        │ total_price      │
        │ status           │
        │ customer (emb)   │ ◄─ Snapshot
        │ products[] (emb) │ ◄─ Line items with snapshot
        │ store_id         │
        │ delivery_id      │ ◄─ For delivery tracking
        └──────────────────┘
               ▲
             (denormalized)
               │
        ┌──────┴──────┐
        │             │
   ┌─────────┐   ┌──────────┐
   │CUSTOMER │   │ PRODUCTS │
   └─────────┘   └──────────┘
   (snapshot)    (snapshots per item)
```

---

## 🔍 Use Cases & Data Structure

**Order Confirmation Workflow:**
1. Customer confirms order → Order page loads
2. Display order #ID with all items and pricing
3. Show delivery/pickup address with special notes
4. Track order status (Paid/Delivered)

**Core Structure:**
- **orders**: Main collection with embedded customer & products
- **customer** (embedded): Name, address, contact, floor/door
- **products** (embedded array): Item details, quantities, prices
- **store**: Reference to managing store
- **delivery** (reference): Delivery tracking info if applicable

**Denormalization Strategy:**
- Customer info embedded for instant display
- Product snapshots (name, category, price) embedded per order
- No joins needed for order confirmation page

---

## 🎨 Design Decisions

| Decision | Rationale |
|----------|-----------|
| **Embedded Customer** | Avoid join - customer data frozen at order time |
| **Embedded Products** | Historical snapshot of products as ordered |
| **Product Snapshots** | Price/name may change - preserve order-time state |
| **Delivery Type Enum** | Validate pickup vs home delivery |
| **Order Status Tracking** | Simple boolean for Paid/Delivered states |
| **Special Notes Field** | Capture delivery instructions (elevators, etc.) |

---

## 🚀 Quick Start

```bash
# Navigate to project
cd n2e1-pizzeria

# Start MongoDB
docker-compose up -d

# Load credentials from .env
set -a
source ../.env
set +a

# Import data
mongorestore --uri="mongodb://$MONGO_USER:$MONGO_PASSWORD@localhost:27017/?authSource=admin" \
  --nsInclude="pizzeria_2.*" ./pizzeria_backup

# Test order confirmation query
mongosh "mongodb://$MONGO_USER:$MONGO_PASSWORD@localhost:27017/?authSource=admin"
> use pizzeria_2
> db.orders.findOne({ order_id: 123 })
> db.orders.aggregate([
    { $match: { order_id: 123 } },
    { $project: { 
        "customer.name": 1, 
        "customer.address": 1, 
        "products": 1, 
        "total_price": 1, 
        "delivery_type": 1,
        "notes": 1
      }
    }
  ])
```

---

## 📁 Project Structure

```
n2e1-pizzeria/
├── README.md                       # This file
├── mongoDB/
│   └── pizzeria.json               # Schema with embedded structures
└── schema/
    └── pizzeria_2/
        ├── order.bson
        ├── order.metadata.json
        ├── customer.bson
        ├── customer.metadata.json
        ├── product.bson
        └── product.metadata.json
```

---

## 📝 Sample Order Document

```json
{
  "_id": ObjectId("..."),
  "order_id": 123,
  "order_date": "2024-03-23T19:45:00Z",
  "delivery_type": "delivery_in",
  "status": {
    "paid": true,
    "delivered": true
  },
  "customer": {
    "customer_id": 1234,
    "name": "Person",
    "surname": "True",
    "address": {
      "street": "Right Street",
      "number": "42b",
      "floor": "3",
      "door": "I",
      "city": "Barcelona"
    },
    "phone_number": "6281321"
  },
  "products": [
    {
      "quantity": 1,
      "product_id": 101,
      "price": 8.00,
      "product_info": {
        "name": "Pizza Margherita",
        "category": "Thin dough"
      }
    },
    {
      "quantity": 2,
      "product_id": 102,
      "price": 12.00,
      "product_info": {
        "name": "Pizza BBQ",
        "category": "Normal dough"
      }
    }
  ],
  "total_price": 61.00,
  "notes": "We don't have elevation!",
  "store_id": ObjectId("..."),
  "delivery_id": ObjectId("...")
}
```

---

## 📊 Differences: Base vs N2E1 Order View

| Aspect | Base (N2E1-base) | N2E1 (Order View) |
|--------|-----------------|------------------|
| **Focus** | General system | Order confirmation |
| **Optimization** | Balanced | Fast single-query display |
| **Denormalization** | Minimal | Embedded customer & products |
| **Query Pattern** | Multiple lookups | Single document fetch |
| **Data Duplication** | None | Snapshots for frozen history |
| **Use Case** | Admin/System | Customer portal page |

---

**Exercise**: N2E1 - Order Confirmation View with Embedded Data

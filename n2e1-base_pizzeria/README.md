# Food Delivery Platform - Pizzeria (N2E1 - Base)

**Description**: MongoDB database design for an online food delivery platform. This project models a system that manages customer orders, products (pizzas, burgers, drinks), stores, and employees for a pizzeria delivery service.

---

## 📌 Exercise Statement

Design a database for an online food ordering and delivery platform with the following requirements:

**Entities:**
- **Customers**: Unique ID, name, surname, address (street, number, city, zip code, town, province), phone
- **Orders**: Unique ID, date/time, delivery type (home/pickup), product quantities, total price, notes
- **Products**: Unique ID, name, description, image, price (pizzas, burgers, drinks)
- **Pizza Categories**: Dynamic categories that can change throughout the year
- **Stores**: Unique ID, address, zip code, city, province, phone number
- **Employees**: Unique ID, name, surname, NIF, phone, role (cook/delivery_driver), store assignment
- **Deliveries**: For home deliveries: tracking delivery driver and delivery date/time

**Relationships:**
- 1 customer → many orders
- 1 order → many products
- 1 store → many orders, many employees
- 1 employee → 1 store, assigned to deliveries (if driver)

---

## ✨ Features

- ✅ Customer management with complete address & contact info
- ✅ Order management with delivery/pickup types and product tracking
- ✅ Dynamic pizza categories that can be updated
- ✅ Product catalog (pizzas, burgers, drinks) with descriptions & images
- ✅ Store management with multiple employees
- ✅ Employee role distinction (cook vs delivery driver)
- ✅ Delivery tracking with driver assignment & timestamp
- ✅ Order notes for special requests
- ✅ JSON Schema validation at database level

---

## 🛠 Technologies

- **Database**: MongoDB (NoSQL)
- **Containerization**: Docker, Docker Compose
- **Validation**: JSON Schema
- **CLI**: mongosh for database queries
- **Data Format**: BSON dumps for backup/restore

---

## 🚀 Installation & Execution

### 1. Clone the Repository
```bash
cd S203-MongoDB/n2e1-base_pizzeria
```

### 2. Environment Variables
Create or use `.env` file in the project root:
```bash
cp ../.env.example .env
```

Configure MongoDB connection:
```
MONGO_URI=mongodb://admin:mi_password@localhost:27017/?authSource=admin
DB_NAME=pizzeria
```

### 3. Start Application

**Start MongoDB Container:**
```bash
cd ../..
docker-compose up -d
```

**Verify MongoDB is running:**
```bash
docker ps | grep mongodb
```

**Import Database:**
```bash
mongorestore --uri="mongodb://admin:mi_password@localhost:27017/?authSource=admin" \
  --nsInclude="pizzeria.*" ./pizzeria_backup
```

### 4. Testing

**Access MongoDB:**
```bash
mongosh "mongodb://admin:mi_password@localhost:27017/?authSource=admin"
```

**Test Collections:**
```bash
> use pizzeria
> db.customer.findOne()
> db.orders.findOne()
> db.products.find({ type: "pizza" }).limit(5)
> db.store.findOne()
> db.employee.find({ role: "delivery_driver" })
```

**Sample Query - Customer Orders:**
```bash
> db.orders.aggregate([
    { $match: { customer_id: ObjectId("...") } },
    { $lookup: { from: "products", localField: "products.product_id", foreignField: "_id", as: "product_details" } },
    { $lookup: { from: "store", localField: "store_id", foreignField: "_id", as: "store_info" } }
  ])
```

---

## 📸 Demo

### Database Schema Structure

**Collections Overview:**
- **customer** - Customer registry with addresses
- **store** - Store locations and contact
- **employee** - Staff with role assignments
- **products** - Catalog (pizzas, burgers, drinks)
- **categories** - Pizza categories (dynamic)
- **orders** - Order history with tracking
- **deliveries** - Delivery assignments for home orders

### Sample Data

**Customer:**
```json
{
  "name": "John",
  "surname": "Doe",
  "address": { "street": "Main St", "number": "42", "city": "Barcelona", "zip_code": "08002" },
  "phone_number": "934123456"
}
```

**Order:**
```json
{
  "customer_id": ObjectId("..."),
  "store_id": ObjectId("..."),
  "products": [
    { "product_id": ObjectId("..."), "quantity": 2, "price": 12.99 }
  ],
  "total_price": 25.98,
  "order_date": "2024-03-23T19:30:00Z",
  "delivery_type": "home", // or "pickup"
  "notes": "Extra cheese on pizza"
}
```

---

## 🧩 Diagrams & Technical Decisions

### ER Diagram - Food Delivery System

```
┌──────────────┐
│   CUSTOMER   │
├──────────────┤
│ _id          │
│ name         │
│ surname      │
│ address      │
│ phone        │
└─────────┬────┘
          │ (places)
          ▼
    ┌──────────────┐         ┌──────────────┐
    │    ORDERS    │◄────────┤   PRODUCTS   │
    ├──────────────┤         ├──────────────┤
    │ _id          │         │ _id          │
    │ customer_id  │ ◄──┐    │ name         │
    │ store_id     │    │    │ description  │
    │ products[]   │────┤    │ price        │
    │ total_price  │    │    │ type (enum)  │
    │ order_date   │    │    │ category_id  │
    │ delivery_id  │    │    └──────────────┘
    │ notes        │    │
    └──────┬───────┘    │
           │            │
    ┌──────┴──────┐     │
    │ (managed)   │     │
    ▼             └─────┘
┌──────────────┐
│    STORE     │
├──────────────┤
│ _id          │
│ address      │
│ phone_number │
└─────────┬────┘
          │ (employs)
          ▼
    ┌──────────────┐
    │   EMPLOYEE   │
    ├──────────────┤
    │ _id          │
    │ name         │
    │ surname      │
    │ nif          │
    │ role (enum)  │ ◄──┐ (for drivers)
    │ store_id     │    │
    └──────────────┘    │
                        │
                ┌───────┴────────┐
                │                ▼
            ┌────────────────┐
            │   DELIVERIES   │
            ├────────────────┤
            │ _id            │
            │ order_id       │
            │ driver_id      │
            │ delivery_date  │
            │ delivery_time  │
            └────────────────┘
```

### Design Rationale

| Decision | Rationale |
|----------|-----------|
| **Separate Collections** | Each entity is independent (customer, store, product, employee) |
| **ObjectId References** | Foreign keys link collections (customer_id in orders, etc.) |
| **Embedded Address** | Address grouped as nested object (street, city, zip_code) |
| **Enum for Type/Role** | Validated at schema level (pizza/burger/drink, cook/driver) |
| **Products Array** | Orders contain product references (no separate order-items table) |
| **Separate Deliveries** | Only home deliveries tracked with driver assignment |
| **Category Reference** | Pizzas reference category_id for dynamic category updates |
| **Phone Validation** | Regex pattern enforces 9-digit phone numbers |
| **NIF Format** | Employee NIF follows format: 8 digits + 1 letter |

### Collections Design

**1. Customer → Orders (1:N)**
- Customer places many orders
- Each order references single customer_id

**2. Products → Orders (M:N)**
- Many products in one order
- Orders embed products array with quantities

**3. Store → Orders (1:N)**
- Store manages many orders
- Each order references store_id

**4. Store → Employees (1:N)**
- Store employs many staff
- Each employee references store_id

**5. Employee → Deliveries (1:N)**
- Delivery driver completes multiple deliveries
- Each delivery references driver_id (if employee role = "delivery_driver")

---

## 📁 Project Structure

```
n2e1-base_pizzeria/
├── README.md                       # This file
├── mongoDB/
│   └── pizzeria.json               # Complete schema with validation
└── schema/
    └── pizzeria/
        ├── customer.bson           # Customer dump
        ├── customer.metadata.json
        ├── store.bson              # Store dump
        ├── store.metadata.json
        ├── employee.bson           # Employee dump
        ├── employee.metadata.json
        ├── products.bson           # Products dump
        ├── products.metadata.json
        ├── categories.bson         # Categories dump
        ├── categories.metadata.json
        ├── orders.bson             # Orders dump
        ├── orders.metadata.json
        ├── deliveries.bson         # Deliveries dump
        └── deliveries.metadata.json
```

---

## 🔗 Database Connection

```
URI: mongodb://admin:mi_password@localhost:27017/?authSource=admin
Database: pizzeria
Port: 27017
Auth: Yes (admin/mi_password)
```

---

## 📝 Key Features Implemented

✅ **Customer Management** - Full customer profiles with addresses  
✅ **Order Tracking** - Complete order lifecycle from placement to delivery  
✅ **Product Catalog** - Pizzas with categories, burgers, drinks  
✅ **Store Network** - Multiple store locations management  
✅ **Employee Roster** - Staff with role-based assignments  
✅ **Delivery System** - Driver assignment & delivery timestamps  
✅ **Schema Validation** - JSON Schema constraints at DB level  
✅ **Data Integrity** - NIF format, phone validation, enum constraints

---

**Last updated**: March 23, 2026  
**Level**: 2 - Intermediate  
**Exercise**: N2E1 - Food Delivery Platform Base Design

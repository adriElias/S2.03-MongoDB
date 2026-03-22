# Optical Store "Cul d'Ampolla" - MongoDB Database Management

**Description**: MongoDB database management system for an optical store that enables computerized management of clients, employees, glasses, suppliers, and sales. The project implements a complete data structure with JSON schema validation to ensure data integrity.

---

## 📌 Exercise Statement

The optical store "Cul d'Ampolla" wants to computerize the management of clients and glasses sales.

**Requirements:**

- **Suppliers**: Name, complete address (street, number, floor, door, city, postal code, country), phone, fax, and NIF.
- **Glasses**: Brand, graduation (left and right lens), frame type (rimless, plastic, metallic), frame color, color of each lens, and price.
- **Clients**: Name, postal address, phone, email, registration date, and reference of the client who recommended them (if applicable).
- **Sales**: Employee who made the sale, client, glasses sold, date, and time of sale.
- **Employees**: Name and identification data.

**Objective**: Design a database that efficiently manages all this information with appropriate relationships between collections.

---

## ✨ Features

- ✅ Supplier management with complete address and validated contact information
- ✅ Glasses catalog with technical details (graduation, frame, lens colors)
- ✅ Client registry with registration date and referrers
- ✅ Sales history with employee, client, product, date, and time
- ✅ JSON schema validation to ensure data integrity
- ✅ Relationships between collections through ObjectId
- ✅ Data dumps in BSON format for backup and import
- ✅ Docker container for MongoDB preconfigured

---

## 🛠 Technologies

- **Database**: MongoDB (NoSQL)
- **Containerization**: Docker, Docker Compose
- **Validation**: JSON Schema
- **Export**: mongodump (BSON format)
- **Authentication**: MongoDB with credentials

---

## 🚀 Installation & Execution

### 1. Clone the repository
```bash
git clone <repository-url>
cd proyectos/sprint2/S203-MongoDB
```

### 2. Environment Variables
The project includes a `.env.example` template file with all required configuration. To set up your environment:

```bash
# Copy the example file to create your local .env
cp .env.example .env
```

Then edit `.env` with your actual credentials:
- **MONGO_INITDB_ROOT_USERNAME**: `admin`
- **MONGO_INITDB_ROOT_PASSWORD**: Your secure password
- **MONGO_PORT**: `27017`
- **MONGO_INITDB_DATABASE**: `optical_store`

**Important:** The `.env` file is listed in `.gitignore` to protect sensitive credentials from being committed to the repository.

### 3. Application Execution

#### Start MongoDB with Docker:
```bash
docker-compose up -d
```

#### Verify MongoDB is running:
```bash
docker ps | grep mongodb
```

#### Access MongoDB (if you have mongosh installed):
```bash
mongosh "mongodb://admin:mi_password@localhost:27017/?authSource=admin"
```

#### Import data (if you have dumps):
```bash
mongorestore --uri="mongodb://admin:mi_password@localhost:27017/?authSource=admin" --nsInclude="optical_store.*" ./optical_store_backup
```

#### Export backup data:
```bash
mongodump --uri="mongodb://admin:mi_password@localhost:27017/?authSource=admin" --db=optical_store --out=./optical_store_backup
```

### 4. Tests
```bash
# Verify collections
mongosh "mongodb://admin:mi_password@localhost:27017/?authSource=admin" \
  --eval "use optical_store; db.providers.countDocuments(); db.glasses.countDocuments(); db.clients.countDocuments(); db.employees.countDocuments(); db.sales.countDocuments();"
```

---

## 📸 Demo

### Database Structure

#### Available Collections:
1. **providers** - Glasses suppliers
2. **glasses** - Glasses catalog
3. **clients** - Registered clients
4. **employees** - Optical store employees
5. **sales** - Sales history

#### Available Files:
- `base_optical_store/mongoDB/optical_store.json` - JSON schema with validation
- `base_optical_store/schema/optical_store/` - Exports in BSON format
  - `clients.bson` / `clients.metadata.json`
  - `employees.bson` / `employees.metadata.json`
  - `glasses.bson` / `glasses.metadata.json`
  - `prelude.json` - Initial data structure
  - `providers.bson` / `providers.metadata.json`
  - `sales.bson` / `sales.metadata.json`

---

## 🧩 Diagrams & Technical Decision Justification

### Entity Relationship (ER) Diagram

```
┌─────────────────────┐
│    PROVIDERS        │
├─────────────────────┤
│ _id (ObjectId)      │
│ name (string)       │
│ address (object)    │
│ phone_number        │
│ fax                 │
│ nif (unique)        │
└──────────┬──────────┘
           │
           │ (referenced by)
           ▼
┌─────────────────────┐
│     GLASSES         │
├─────────────────────┤
│ _id (ObjectId)      │
│ provider_id (ref)   │
│ brand (string)      │
│ graduation (object) │
│ frame_type (enum)   │
│ frame_color         │
│ lens_color (object) │
│ price (double)      │
└──────────┬──────────┘
           │
           │ (referenced by)
           ▼
┌─────────────────────────┐
│       SALES             │
├─────────────────────────┤
│ _id (ObjectId)          │
│ employee_id (ref)       │
│ client_id (ref)         │
│ glasses_id (ref)        │
│ sale_date (timestamp)   │
│ sale_time (string)      │
└─────────────────────────┘
           ▲
           │
    ┌──────┴──────┐
    ▼             ▼
┌─────────┐  ┌─────────────┐
│EMPLOYEES│  │   CLIENTS   │
├─────────┤  ├─────────────┤
│ _id     │  │ _id         │
│ name    │  │ name        │
│         │  │ address     │
│         │  │ phone       │
│         │  │ email       │
│         │  │ registered_at
│         │  │ referred_by │
└─────────┘  └─────────────┘
```

### Technical Decisions

#### 1. **Choosing MongoDB (NoSQL) instead of MySQL**
- ✅ **Flexibility**: BSON documents allow complex nested structures (addresses as objects)
- ✅ **Scalability**: Horizontal design through sharding
- ✅ **Validation**: JSON Schema validated at database level
- ✅ **Queries**: Native support for relationships through `$lookup`

#### 2. **Collection Structure**
- **Separation by logical table**: Each collection represents a main entity
- **Controlled denormalization**: Nested objects (address, graduation) within documents for closely related data
- **Normalization with references**: Use `ObjectId` for 1:N relationships between collections

#### 3. **Validation with JSON Schema**
```json
{
  "bsonType": "object",
  "required": ["name", "nif"],
  "properties": { ... }
}
```
- Ensures data integrity at database level
- Validates enums, regex patterns (NIF, phone), min/max values

#### 4. **Enumerations & Validations**
- **frame_type**: Enum `["metallic", "plastic", "rimless"]` to prevent inconsistent values
- **NIF**: Regex validation `^[A-Z][0-9]{8}$`
- **Phone**: Exactly 9 digits

#### 5. **BSON Export**
- Compact binary format for backups
- Preserves MongoDB data types (dates, ObjectId)
- Enables automated migration and backup

#### 6. **Docker for Infrastructure**
- Isolation and portability
- Preconfigured environment
- Facilitates coexistence with other services

---

## 📁 Project Structure

```
S203-MongoDB/
├── .env                            # Environment configuration (gitignored)
├── .env.example                    # Environment template
├── .gitignore                      # Git ignore rules
├── docker-compose.yml              # Docker compose configuration
├── README.md                       # Main project documentation
├── base_optical_store/             # Base implementation (Exercise 1)
│   ├── README.md
│   ├── doc/                        # Documentation
│   ├── mongoDB/
│   │   └── optical_store.json      # Complete schema with validations
│   └── schema/
│       └── optical_store/
│           ├── clients.bson          # Clients dump
│           ├── clients.metadata.json
│           ├── employees.bson        # Employees dump
│           ├── employees.metadata.json
│           ├── glasses.bson          # Glasses dump
│           ├── glasses.metadata.json
│           ├── prelude.json          # Initial data structure
│           ├── providers.bson        # Providers dump
│           ├── providers.metadata.json
│           ├── sales.bson            # Sales dump
│           └── sales.metadata.json
├── n1e1-opticalStore/              # Exercise 1 variant
│   └── [similar structure to base_optical_store]
└── n1e2-opticalStore/              # Exercise 2 variant
    └── [similar structure to base_optical_store]
```

---

## 🔧 Useful Commands

### View database and collections
```bash
mongosh "mongodb://admin:mi_password@localhost:27017/?authSource=admin"
> use optical_store
> show collections
```

### Count documents per collection
```bash
> db.providers.countDocuments()
> db.glasses.countDocuments()
> db.clients.countDocuments()
> db.employees.countDocuments()
> db.sales.countDocuments()
```

### Query with relationships
```bash
# Sales with client and glasses details
> db.sales.aggregate([
    { $lookup: { from: "clients", localField: "client_id", foreignField: "_id", as: "client_details" } },
    { $lookup: { from: "glasses", localField: "glasses_id", foreignField: "_id", as: "glasses_details" } },
    { $limit: 5 }
  ])
```

### Stop MongoDB
```bash
docker-compose down
```

---

## 👥 Author
Database design exercise for optical store management.

---

**Last updated**: March 22, 2026

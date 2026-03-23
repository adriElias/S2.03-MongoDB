# Optical Store "Cul d'Ampolla" - Customer View (N1E1)

**Description**: MongoDB database design from the customer's perspective of the optical store interface. This exercise (N1E1) focuses on how data relates through the customer's view and interaction patterns.

**Exercise**: N1E1 - Database diagram and design decisions from customer view.

---

## 📌 Overview

This exercise models a database optimized for a customer portal of an optical store, showing how to structure data for efficient customer queries: profile management, purchase history, and glasses search.

---

## 🎯 Features from Customer View

### About Clients
- 📱 **Personal Data Registry** - Name, address, phone, email
- 🔗 **Referrals** - See who referred them to the store
- 👁️ **Purchase History** - All glasses they have purchased
- 📅 **Registration Date** - When they registered

### About Glasses
- 👓 **Brand and Model** - Which brand and model of glasses
- 📊 **Graduation** - Left and right lens
- 🎨 **Visual Appearance** - Frame type, color
- 💰 **Price** - Cost of the glasses
- 🏭 **Provider** - Who supplies them

### About Sales
- 💳 **Transaction** - Which customer bought which glasses
- 👔 **Employee** - Who made the sale
- 📆 **Date and Time** - When it was made

---

## 🛠 Technologies

- **Database**: MongoDB (NoSQL)
- **Containerization**: Docker, Docker Compose
- **Validation**: JSON Schema

---

## 🧩 UX/UI Diagram - Customer View

### 📊 Information Display Diagram

```
┌──────────────────────────────────────────────────────────┐
│         OPTICAL STORE CUL D'AMPOLLA - CUSTOMER PORTAL   │
├──────────────────────────────────────────────────────────┤
│                                                           │
│  ┌─ MY PROFILE ─────────────────────────────────────┐   │
│  │                                                  │    │
│  │  📛 Name: Jaume Martí                           │    │
│  │  📍 Address: Main Street, 15, 2nd, Barcelona   │    │
│  │  📞 Phone: 932345678                           │    │
│  │  📧 Email: jaume.marti@email.com               │    │
│  │  📅 Registered: 15/03/2024                     │    │
│  │  🤝 Referred by: Joan Pérez (2020)             │    │
│  └──────────────────────────────────────────────────┘    │
│                                                           │
│  ┌─ MY PURCHASES ────────────────────────────────────┐  │
│  │                                                  │   │
│  │  1️⃣  Ray-Ban Aviator                           │   │
│  │      • Lens: OD -2.50, OI -2.75               │   │
│  │      • Frame: Metallic Gold                    │   │
│  │      • Price: 180€                             │   │
│  │      • Purchased: 15/03/2024 at 11:30          │   │
│  │      • Employee: Maria García                  │   │
│  │      • Provider: LuxOptical S.A.               │   │
│  │                                                │   │
│  │  2️⃣  Oakley Holbrook                           │   │
│  │      • Lens: OD -1.50, OI -1.25               │   │
│  │      • Frame: Plastic Black                    │   │
│  │      • Price: 220€                             │   │
│  │      • Purchased: 28/06/2024 at 15:45          │   │
│  │      • Employee: Albert López                  │   │
│  │      • Provider: SportSight Ltd                │   │
│  └──────────────────────────────────────────────────┘   │
│                                                           │
│  ┌─ SEARCH NEW GLASSES ──────────────────────────────┐  │
│  │ 🔍 Filter: [Brand] [Max Price] [Frame Type]    │  │
│  │                                                  │  │
│  │ ✨ REC: Oakley Sutro - 195€ (Rimless Frame)    │  │
│  │ ✨ REC: Gucci GG0849S - 350€ (Plastic Frame)   │  │
│  └──────────────────────────────────────────────────┘  │
│                                                           │
└──────────────────────────────────────────────────────────┘
```

---

## 📐 ER Diagram from Customer View

```
                         ┌──────────────────┐
                         │     CLIENTS      │
                         ├──────────────────┤
                         │ _id              │
                         │ name             │
                         │ address          │
                         │ phone_number     │
                         │ email            │
                         │ registered_at    │
                         │ referred_by ◄───────┐ (optional)
                         └────────┬─────────┘  │
                                  │            │
                    ┌─────────────┴─────────────┤
                    │                           │
           (see purchases)      (see who
                    │            referred)
                    ▼                           │
            ┌────────────────┐                 │
            │     SALES      │                 │
            ├────────────────┤           Self-Reference
            │ _id            │                 │
            │ client_id ◄────┼─────────────────┘
            │ glasses_id     │
            │ employee_id    │
            │ sale_date      │
            │ sale_time      │
            └────────┬───────┘
                     │
        (see glasses details)
                     │
                     ▼
            ┌────────────────┐
            │    GLASSES     │
            ├────────────────┤
            │ _id            │
            │ brand          │
            │ graduation     │
            │ frame_type     │
            │ frame_color    │
            │ lens_color     │
            │ price          │
            │ provider_id ───┼──► PROVIDERS
            └────────────────┘      (provider info)
```

---

## 🔍 Use Cases & Data Structure

**Customer Workflow:**
1. View personal profile + referrer info
2. Consult complete purchase history with glasses details
3. Search for new glasses (brand, price, frame type filters)

**Core Collections:**
- **clients**: Name, address, email, registration_at, referred_by
- **glasses**: Brand, graduation, frame_type, color, price, provider_id
- **sales**: client_id, glasses_id, employee_id, sale_date, sale_time
- **providers**: Name, address, contact info

---

## 🎨 Design Rationale

| Decision | Rationale |
|----------|-----------|
| **ObjectId References** | Links collections efficiently (1:N relationships) |
| **Nested Address** | Groups related data within documents |
| **Self-Reference (referred_by)** | Creates referral network |
| **Separate Date/Time** | Enables specific date/time filtering |
| **Enum frame_type** | Ensures data consistency |

---

## 📁 Project Structure (N1E1)

```
n1e1-opticalStore/
├── README.md                       # This file
├── doc/                            # Documentation
├── mongoDB/
│   └── customer_view.json          # Schema from customer view
└── schema/
    └── optical_store/
        ├── clients.bson
        ├── clients.metadata.json
        ├── employees.bson
        ├── employees.metadata.json
        ├── glasses.bson
        ├── glasses.metadata.json
        ├── prelude.json
        ├── providers.bson
        ├── providers.metadata.json
        ├── sales.bson
        └── sales.metadata.json
```

---

## 🚀 How to Run

### 1. Navigate to project directory
```bash
# Start MongoDB
docker-compose up -d

# Load credentials from .env
set -a
source ../.env
set +a

# Import data
mongorestore --uri="mongodb://$MONGO_USER:$MONGO_PASSWORD@localhost:27017/?authSource=admin" \
  --nsInclude="optical_store.*" ./optical_store_backup

# Test queries
mongosh "mongodb://$MONGO_USER:$MONGO_PASSWORD@localhost:27017/?authSource=admin"
> use optical_store
> db.clients.findOne()
> db.sales.aggregate([
    { $lookup: { from: "glasses", localField: "glasses_id", foreignField: "_id", as: "glasses" } }
  ])
```

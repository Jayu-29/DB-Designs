# 📦 Instagram Thrift Creator Store — Database Schema

A relational database schema for a creator-run thrift and handmade goods store that accepts orders via **Instagram** and **WhatsApp**. Covers the full order lifecycle — from product listing to payment and last-mile delivery.

---

## 🗂️ Schema Overview

The database is built around **8 tables** and handles two distinct product types:

| Type | Description |
|------|-------------|
| `thrifted` | One-of-a-kind second-hand items, each tracked individually |
| `handmade` | Creator-made items sold in stock quantities |

---

## 🧱 Tables

### 👤 `customer`
Stores buyer information including contact details and full delivery address.

| Column | Type | Notes |
|--------|------|-------|
| `customer_id` | int | Primary Key |
| `name` | varchar(100) | not null |
| `email` | varchar(100) | unique, not null |
| `phone_number` | char(15) | unique, not null |
| `address` | text | |
| `country` | varchar(50) | not null |
| `city` | varchar(50) | not null |
| `state` | varchar(50) | not null |
| `pincode` | varchar(10) | not null |

---

### 🛍️ `products`
Master catalog for all products (both thrifted and handmade).

| Column | Type | Notes |
|--------|------|-------|
| `product_id` | int | Primary Key |
| `product_name` | varchar(100) | not null |
| `product_type` | enum | `'thrifted'`, `'handmade'` |
| `price` | decimal(10,2) | |
| `size` | enum | `XS`, `S`, `M`, `L`, `XL`, `XXL` |
| `color` | varchar(50) | not null |
| `category` | enum | `'men'`, `'women'`, `'unisex'` |
| `condition` | enum | `'new'`, `'almost_new'`, `'very_good'`, `'good'`, `'satisfactory'`, `'vintage'` |
| `brand` | varchar(50) | |
| `description` | text | |
| `img_url` | varchar(255) | |

---

### 👕 `thrifted_items`
Tracks each unique thrifted item and its availability status.

| Column | Type | Notes |
|--------|------|-------|
| `thrift_id` | int | Primary Key |
| `product_id` | int | FK → `products` |
| `status` | enum | `'available'`, `'sold'`, `'reserved'` |

---

### 🧵 `handmade_items`
Tracks handmade items with stock quantity.

| Column | Type | Notes |
|--------|------|-------|
| `handmade_id` | int | Primary Key |
| `product_id` | int | FK → `products` |
| `qty_available` | int | not null |

---

### 📦 `orders`
Order header — captures source platform, status, and total.

| Column | Type | Notes |
|--------|------|-------|
| `order_id` | int | Primary Key |
| `source` | enum | `'instagram'`, `'whatsapp'` |
| `order_date` | datetime | |
| `order_status` | enum | `'order_received'`, `'pending'`, `'order_confirmed'`, `'shipped'`, `'delivered'`, `'cancelled'` |
| `total_amount` | decimal(10,2) | |
| `customer_id` | int | FK → `customer` |

---

### 📋 `order_items`
Line items — links each order to its products with quantities.

| Column | Type | Notes |
|--------|------|-------|
| `order_item_id` | int | Primary Key |
| `order_id` | int | FK → `orders` |
| `product_id` | int | FK → `products` |
| `qty` | int | |

---

### 💳 `payments`
Payment details for each order.

| Column | Type | Notes |
|--------|------|-------|
| `payment_id` | int | Primary Key |
| `order_id` | int | FK → `orders` |
| `payment_mode` | enum | `'upi'`, `'card'`, `'net_banking'`, `'cod'` |
| `status` | enum | `'pending'`, `'paid'`, `'failed'` |
| `date` | timestamp | |

---

### 🚚 `shipping`
Shipping and logistics tracking per order.

| Column | Type | Notes |
|--------|------|-------|
| `shipping_id` | int | Primary Key |
| `tracking_id` | int | |
| `order_id` | int | FK → `orders` |
| `status` | enum | `'in_process'`, `'dispatched'`, `'on_the_way'`, `'delivered'` |
| `logistic_provider` | varchar(150) | |
| `dispatched_date` | datetime | |
| `delivery_date` | datetime | |

---


| Relationship | Type |
|---|---|
| `customer` → `orders` | One-to-Many |
| `orders` → `order_items` | One-to-Many |
| `products` → `order_items` | One-to-Many |
| `products` → `thrifted_items` | One-to-One |
| `products` → `handmade_items` | One-to-One |
| `orders` → `payments` | One-to-One |
| `orders` → `shipping` | One-to-One |

---

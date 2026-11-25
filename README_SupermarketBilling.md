# 🛒 Supermarket Billing System

A **console (terminal) based** supermarket billing application written in **core Java**. It supports role-based login (Admin / Cashier), product inventory, customer entry, a shopping cart, discount and tax calculation, multiple payment modes, loyalty points and printable invoices. It uses only the JDK, with no external libraries or database.

---

## Table of Contents

1. [Features](#features)
2. [Tech Stack](#tech-stack)
3. [Requirements](#requirements)
4. [Getting Started](#getting-started)
5. [Login Credentials](#login-credentials)
6. [Roles & Menus](#roles--menus)
7. [Typical Billing Workflow](#typical-billing-workflow)
8. [Sample Data](#sample-data)
9. [Billing Calculations](#billing-calculations)
10. [Validations & Business Rules](#validations--business-rules)
11. [Sample Invoice](#sample-invoice)
12. [Project Structure](#project-structure)
13. [Class Reference](#class-reference)
14. [Concepts Demonstrated](#concepts-demonstrated)
15. [Known Limitations](#known-limitations)
16. [Future Improvements](#future-improvements)

---

## Features

- 🔐 Role-based login: **Admin** and **Cashier**
- 📦 Product inventory: add, view and search by barcode or product ID
- 👤 Customer details capture (name and phone)
- 🛍️ Shopping cart with stock checking
- 💸 Flat discount support
- 🧾 Automatic 5% tax calculation
- 💳 Payment modes: Cash, UPI, Card, with a confirm/cancel step
- 📉 Stock deducted **only after payment is confirmed**
- 🎁 Loyalty points (1 point per Rs. 100 of the bill total)
- 📄 Formatted invoice printed in the terminal
- 📊 Sales summary (bill count and total revenue) for Admin
- ✅ Input validation and graceful handling of invalid input

---

## Tech Stack

| Item | Details |
|---|---|
| Language | Java |
| Interface | Console / Terminal (`Scanner`) |
| Data storage | In-memory (`ArrayList`) |
| Dependencies | None (JDK only) |

---

## Requirements

- **JDK 8 or higher** (JDK 17+ recommended)
- A terminal or command prompt, or any Java IDE

```bash
java -version
javac -version
```

---

## Getting Started

### 1. Save the file

The file contains `public class abc`, so it **must be saved as `abc.java`**.

### 2. Compile

```bash
javac abc.java
```

### 3. Run

```bash
java abc
```

> `java SupermarketBillingSystem` also works, since that class has its own `main` method. `abc` simply forwards to it.

### Running in an IDE

Create a Java project, add `abc.java`, and run the `main` method of the `abc` class (IntelliJ IDEA, Eclipse, NetBeans, VS Code).

---

## Login Credentials

| Role | Username | Password |
|---|---|---|
| Admin | `admin` | `admin` |
| Cashier | `cashier` | `cashier` |

At the login menu, choose the role first (1 = Admin, 2 = Cashier), then enter the matching username and password. A mismatched role and username will be rejected.

---

## Roles & Menus

### Login Menu
```
1. Login as Admin
2. Login as Cashier
3. Exit
```

### Admin Dashboard
```
 1. Add Product        6. Add To Cart
 2. View Products      7. View Cart
 3. Search Product     8. Generate Bill / Pay
 4. Sales Summary      9. Clear Cart
 5. New Customer      10. Logout
```

### Cashier Dashboard
```
 1. Search Product     5. View Cart
 2. View Products      6. Generate Bill / Pay
 3. New Customer       7. Clear Cart
 4. Add To Cart        8. Logout
```

### Permission Comparison

| Feature | Admin | Cashier |
|---|:---:|:---:|
| Add product | ✅ | ❌ |
| Sales summary | ✅ | ❌ |
| View / search products | ✅ | ✅ |
| New customer | ✅ | ✅ |
| Cart operations | ✅ | ✅ |
| Generate bill / payment | ✅ | ✅ |

---

## Typical Billing Workflow

1. Log in as **Cashier** (or Admin).
2. Choose **New Customer** and enter the customer name and phone.
3. Choose **Add To Cart**, then enter a barcode or product ID and a quantity. Repeat for more items.
4. Choose **View Cart** to review items and the subtotal.
5. Choose **Generate Bill / Pay**:
   - Enter a discount amount (`0` for none).
   - Select the payment mode (Cash / UPI / Card).
   - Confirm with `1` (payment successful) or `2` (cancel).
6. The invoice is printed, stock is updated, and the cart and customer are reset for the next sale.

---

## Sample Data

Three products are preloaded at startup:

| Product ID | Name | Barcode | Price (Rs.) | Stock |
|---|---|---|---:|---:|
| P101 | Rice | 8901001 | 60.00 | 50 |
| P102 | Sugar | 8901002 | 45.00 | 40 |
| P103 | Oil | 8901003 | 150.00 | 25 |

Products can be looked up by **barcode** or **product ID** (the ID is case-insensitive).

---

## Billing Calculations

```
Subtotal = Σ (price × quantity)
Taxable  = Subtotal − Discount
Tax      = Taxable × 5%
Total    = Taxable + Tax
Points   = (int) (Total / 100)
```

**Example:** 2 × Rice (60) + 1 × Oil (150), discount Rs. 20

| Step | Value |
|---|---:|
| Subtotal | 270.00 |
| Discount | 20.00 |
| Taxable | 250.00 |
| Tax (5%) | 12.50 |
| **Total** | **262.50** |
| Loyalty points earned | 2 |

---

## Validations & Business Rules

**Login**
- Role must match the correct username/password pair.
- Invalid menu choices are rejected without crashing.

**Add Product (Admin)**
- ID, name and barcode cannot be empty.
- Price must be greater than 0 and stock cannot be negative.
- Price and stock must be numeric.
- Product ID (case-insensitive) and barcode must be unique.

**Customer**
- Name and phone are both required.

**Cart**
- Barcode/ID must exist and quantity must be a positive whole number.
- Stock check includes quantities already in the cart, so you cannot add more than what is available.

**Billing**
- Cart must not be empty and customer details must be entered first.
- Discount must be between 0 and the subtotal.
- An invalid payment choice defaults to **Cash**.
- If payment is cancelled (anything other than `1`), the stock is **not** changed and the cart is kept.
- On success: stock is reduced, the bill is stored, loyalty points are added, then the cart and current customer are cleared.

**Session**
- Logout clears the role, current customer and cart.
- Closing the input stream (EOF) exits cleanly.

---

## Sample Invoice

```
======================================
           SUPERMARKET BILL
======================================
Bill ID  : B1001
Customer : Ravi
Phone    : 9876543210
--------------------------------------
Product            Qty  Amount (Rs)
--------------------------------------
Rice                 2       120.00
Oil                  1       150.00
--------------------------------------
Subtotal:       Rs.     270.00
Discount:       Rs.      20.00
Tax (5%):       Rs.      12.50
TOTAL:          Rs.     262.50
Payment Mode:   UPI
Loyalty Points: 2
======================================
       Thank you! Visit again.
======================================
```

---

## Project Structure

```
abc.java
├── Product                     → model: inventory item
├── Customer                    → model: customer with loyalty points
├── CartItem                    → model: product + quantity
├── Bill                        → model: completed sale and invoice generator
├── SupermarketBillingSystem    → application logic, menus, input handling
└── abc (public)                → entry-point wrapper calling SupermarketBillingSystem.main
```

---

## Class Reference

| Class | Responsibility | Key members |
|---|---|---|
| `Product` | Holds product data | `reduceStock(int)`, `toString()` |
| `Customer` | Customer info and loyalty | `addPoints(int)`, `getLoyaltyPoints()` |
| `CartItem` | One cart line | `getAmount()` returns price × quantity |
| `Bill` | Snapshot of a sale | `createInvoice()` builds the formatted receipt |
| `SupermarketBillingSystem` | Controller and UI | `start()`, `loginMenu()`, `dashboardMenu()`, `addProduct()`, `searchProduct()`, `addToCart()`, `generateBill()`, `salesSummary()` |
| `abc` | Public entry class | `main()` delegates to `SupermarketBillingSystem.main()` |

**Key fields in `SupermarketBillingSystem`:** `products`, `cart`, `bills` (all `ArrayList`), `currentCustomer`, `currentRole`, `billCounter` (starts at `1001`).

---

## Concepts Demonstrated

| Concept | Where |
|---|---|
| Classes & Objects | `Product`, `Customer`, `CartItem`, `Bill` |
| Encapsulation | Private fields with getters |
| Composition | `Bill` has a `Customer` and `CartItem`s; `CartItem` has a `Product` |
| Collections | `ArrayList` for products, cart, bills |
| Method overriding | `Product.toString()` |
| Exception handling | `NumberFormatException` handling for numeric input |
| String formatting | `String.format`, `printf`, `StringBuilder` |
| Control flow | Menu loops, `switch`, role-based branching |
| Defensive copy | `Bill` copies the cart list in its constructor |
| Console I/O | `Scanner` with `hasNextLine()` checks |

---

## Known Limitations

- All data is **in memory**: products, bills and sales are lost when the program exits.
- Credentials are **hard-coded** and stored in plain text (demo only).
- Each `New Customer` creates a fresh customer, so **loyalty points are not carried over** between purchases.
- Loyalty points are not redeemable.
- Adding the same product twice creates **separate cart lines** instead of merging quantities.
- Items cannot be removed individually from the cart; only the whole cart can be cleared.
- Discount is a **flat amount** only (no percentage option).
- No edit/delete for products, and no restocking option.
- Sales summary shows only bill count and total revenue.
- Tax rate (5%) and loyalty rule are hard-coded.
- A failed login hints at demo credentials but does not display them.
- Requires `abc.java` as the file name because of the public class.

---

## Future Improvements

- Persist data using files or a database (JDBC, SQLite, MySQL)
- Secure authentication with hashed passwords and a user table
- Returning-customer lookup by phone number and loyalty redemption
- Edit/remove cart items and merge duplicate lines
- Percentage discounts and per-product tax rates
- Product update, delete and restock options
- Save invoices to text/PDF files
- Daily/monthly sales reports and low-stock alerts
- Unit tests for billing calculations
- Separate classes into individual files/packages (MVC structure)

---

## License

Free to use for educational purposes.

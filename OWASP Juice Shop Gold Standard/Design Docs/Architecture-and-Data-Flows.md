# Architecture & Data Flow Diagrams

**Document Version:** 1.0  
**Status:** Final  
**Author:** Abhishek Shrivastav  
**Date:** August 12, 2025

---

## 1. High-Level Architecture

The OWASP Juice Shop is a **modern, monolithic web application** following a classic **three-tier architecture**:

- **Client-Side (Frontend):**
  - Responsive SPA built with **Angular**.
  - Runs in the user’s browser.
  - Handles UI rendering & interactions.

- **Server-Side (Backend):**
  - RESTful API built with **Node.js & Express**.
  - Handles business logic, data processing, and communication with data stores.
  - Exposes `/logs/` endpoint for log file access.

- **Data Stores (Hybrid Model):**
  - **SQLite** (lightweight relational DB) for users, products, baskets.
  - **MongoDB** for unstructured data (reviews, feedback).

```mermaid
graph TD
    subgraph "User's Browser"
        A[Angular Frontend]
    end
    subgraph "Server Environment"
        B[Node.js / Express Backend]
        C[SQLite Database]
        D[MongoDB Database]
    end
    A -- REST API Calls (HTTPS) --> B
    B -- SQL Queries --> C
    B -- NoSQL Queries --> D
```

**Trust Boundaries:**
- **Internet → Client** (user to frontend)
- **Client → Server** (critical, must validate all inputs)
- **Server → Data Stores** (app to DBs)

---

## 2. Context Diagram (Level 0 DFD)

The Juice Shop as a single process and its external interactions:

```mermaid
graph TD
    User[Customer / Visitor] -- Interacts via Browser --> JuiceShop((Juice Shop System))
    Admin[Administrator] -- Manages via Browser --> JuiceShop
    PartnerSystem[Partner Catalog System] -- XML Catalogs --> JuiceShop
    JuiceShop -- User Data / Orders --> DBs[(Application Databases)]
    JuiceShop -- "Transaction Hashes (undocumented)" --> Ledger[(Public Blockchain Ledger)]
```

---

## 3. Detailed Data Flow Diagrams (DFDs)

### 3.1 Product Search (User Story 2.1)

```mermaid
graph TD
    subgraph "Browser (Client)"
        Search[Search Bar]
    end
    subgraph "Backend Server"
        API(API Endpoint: /rest/products/search)
        Query{Construct DB Query}
        DBQuery(Execute Search)
    end
    subgraph "SQL Data Store"
        Products[(Products Table)]
    end
    Search -- "1. User enters raw search term" --> API
    API -- "2. Raw search term" --> Query
    Query -- "3. SQL LIKE query" --> DBQuery
    DBQuery -- "4. Query" --> Products
    Products -- "5. Product Results" --> DBQuery
    DBQuery -- "6. Results" --> API
    API -- "7. JSON Response" --> Search
```

---

### 3.2 Viewing Shopping Basket (User Story 3.2)

```mermaid
graph TD
    subgraph "Browser (Client)"
        ViewBasket[Click 'Your Basket']
    end
    subgraph "Backend Server"
        BasketAPI(API Endpoint: /rest/basket/:basketId)
        AuthCheck{Verify User Ownership}
        GetData(Retrieve Basket Items)
    end
    subgraph "SQL Data Store"
        BasketItems[(BasketItems Table)]
    end
    ViewBasket -- "1. GET Request" --> BasketAPI
    BasketAPI -- "2. basketId + userId" --> AuthCheck
    AuthCheck -- "3. Authorization Check" --> GetData
    GetData -- "4. SELECT * FROM BasketItems" --> BasketItems
    BasketItems -- "5. Basket Contents" --> GetData
    GetData -- "6. Formatted Data" --> BasketAPI
    BasketAPI -- "7. JSON Response" --> ViewBasket
```

---

### 3.3 Updating Profile Picture from URL (User Story 1.4)

```mermaid
graph TD
    subgraph "Browser (Client)"
        A[Profile Page URL Input]
    end
    subgraph "Backend Server"
        B(API Endpoint: /api/user/profile-image-url)
        C{Fetch Image}
        D(Save & Associate Image)
    end
    subgraph "External Service"
        E[External Image Host]
    end
    A -- "1. Submit image URL" --> B
    B -- "2. Image URL" --> C
    C -- "3. HTTP GET to host" --> E
    E -- "4. Image Data" --> C
    C -- "5. Raw Image Data" --> D
```

---

### 3.4 Liking a Product Review (User Story 4.1)

```mermaid
graph TD
    subgraph "Browser (Client)"
        A[User clicks 'Like']
    end
    subgraph "Backend Server"
        B(API Endpoint: /api/reviews/like)
        C{Update Like Count}
    end
    subgraph "NoSQL Data Store"
        D[(Reviews Collection)]
    end
    A -- "1. API call" --> B
    B -- "2. 200 OK Response" --> A
    B -- "3. Async Update" --> C
    C -- "4. $inc likes" --> D
```

---

### 3.5 B2B Bulk Order Upload (User Story 5.2)

```mermaid
graph TD
    subgraph "Browser (Admin)"
        A[Admin Uploads File]
    end
    subgraph "Backend Server"
        B(API Endpoint: /api/b2b/order)
        C{Parse File}
        D(Create Orders)
    end
    subgraph "Data Store"
        E[(Orders Table)]
    end
    A -- "1. Upload JSON/XML" --> B
    B -- "2. Raw File Data" --> C
    C -- "(Processes external entities in XML)" --> B
    C -- "3. Parsed Data" --> D
    D -- "4. INSERT into Orders" --> E

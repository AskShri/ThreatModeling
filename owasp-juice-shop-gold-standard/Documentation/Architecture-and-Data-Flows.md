# Architecture & Data Flow Diagrams

- **Document Version**: 1.4
- **Status**: Final
- **Author**: Abhishek Shrivastav
- **Date**: August 12, 2025

---

## 1. High-Level Architecture

The OWASP Juice Shop is a modern, monolithic web application built primarily in JavaScript/TypeScript. It follows a classic three-tier architecture.

-   **Client-Side (Frontend)**: A responsive Single Page Application (SPA) built with Angular. It runs entirely in the user's web browser and is responsible for all UI rendering and user interaction.
-   **Server-Side (Backend)**: A RESTful API built with Node.js and the Express framework. It handles all business logic, data processing, and communication with the data stores. For administrative troubleshooting, the server exposes an endpoint to serve log files directly from the `/logs/` directory.
-   **Data Stores (Hybrid Model)**:
    -   **Primary Database**: A lightweight **SQLite** file-based database serves as the primary persistence layer for core transactional data, including users, products, and baskets.
    -   **Document Store**: For unstructured data like product reviews, the application leverages a **MongoDB** document store.

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
Key Trust BoundariesInternet to Client: The user's interaction with the frontend.Client to Server: This is the most critical trust boundary. All data coming from the Angular client to the Node.js backend is considered untrusted and must be validated.Server to Data Stores: The boundary between the application logic and the persistence layers.2. Context Diagram (Level 0 DFD)This diagram shows the Juice Shop as a single process and illustrates its interactions with external entities.graph TD
    User[Customer / Visitor] -- Interacts via Browser --> JuiceShop((Juice Shop System))
    Admin[Administrator] -- Manages via Browser --> JuiceShop
    PartnerSystem[Partner Catalog System] -- XML Catalogs --> JuiceShop
    JuiceShop -- User Data / Orders --> DBs[(Application Databases)]
    JuiceShop -- "Transaction Hashes (undocumented)" --> Ledger[(Public Blockchain Ledger)]
3. Detailed Data Flow Diagrams (DFDs)3.1 DFD for Product SearchThis DFD shows how a user's search query is processed against the primary SQL database.graph TD
    subgraph "Browser (Client)"
        Search[Search Bar]
    end
    subgraph "Backend Server"
        API(API Endpoint: /rest/products/search)
        Query{Process: Construct DB Query}
        DBQuery(Process: Execute Search)
    end
    subgraph "SQL Data Store"
        Products[(Products Table)]
    end
    Search -- "1. User enters raw search term" --> API
    API -- "2. Raw search term" --> Query
    Query -- "3. SELECT * FROM Products WHERE name LIKE '%<term>%'" --> DBQuery
    DBQuery -- "4. Query" --> Products
    Products -- "5. Product Results" --> DBQuery
    DBQuery -- "6. Results" --> API
    API -- "7. JSON Response" --> Search
3.2 DFD for Viewing Shopping BasketThis DFD illustrates how a user's basket is retrieved, highlighting a key access control checkpoint.graph TD
    subgraph "Browser (Client)"
        ViewBasket[User clicks 'Your Basket']
    end
    subgraph "Backend Server"
        BasketAPI(API Endpoint: /rest/basket/:basketId)
        AuthCheck{Process: Verify User Ownership}
        GetData(Process: Retrieve Basket Items)
    end
    subgraph "SQL Data Store"
        BasketItems[(BasketItems Table)]
    end
    ViewBasket -- "1. GET Request with basketId from URL" --> BasketAPI
    BasketAPI -- "2. Authenticated User ID & basketId" --> AuthCheck
    AuthCheck -- "3. Authorization Check" --> GetData
    GetData -- "4. SELECT * FROM BasketItems WHERE BasketId = ?" --> BasketItems
    BasketItems -- "5. Basket Contents" --> GetData
    GetData -- "6. Formatted Data" --> BasketAPI
    BasketAPI -- "7. JSON Response" --> ViewBasket
3.3 DFD for Updating Profile Picture from URLThis DFD shows the flow when a user provides a URL to an external image for their profile.graph TD
    subgraph "Browser (Client)"
        A[Profile Page URL Input]
    end
    subgraph "Backend Server"
        B(API Endpoint: /api/user/profile-image-url)
        C{Process: Fetch Image}
        D(Process: Save & Associate Image)
    end
    subgraph "External Service"
        E[External Image Host]
    end
    A -- "1. User submits image URL" --> B
    B -- "2. Image URL" --> C
    C -- "3. HTTP GET request to URL" --> E
    E -- "4. Image Data" --> C
    C -- "5. Raw Image Data" --> D
3.4 DFD for Liking a Product ReviewThis DFD illustrates the asynchronous process for liking a review, which uses the MongoDB data store.graph TD
    subgraph "Browser (Client)"
        A[User clicks 'Like']
    end
    subgraph "Backend Server"
        B(API Endpoint: /api/reviews/like)
        C{Process: Update Like Count}
    end
    subgraph "NoSQL Data Store"
        D[(Reviews Collection)]
    end
    A -- "1. API call with Review ID" --> B
    B -- "2. Immediate 200 OK Response" --> A
    B -- "3. Asynchronous Update Operation" --> C
    C -- "4. findOneAndUpdate({$inc: {likes: 1}})" --> D
3.5 DFD for B2B Bulk Order UploadThis DFD shows how administrators can upload bulk orders in different formats.graph TD
    subgraph "Browser (Admin)"
        A[Admin Uploads Order File]
    end
    subgraph "Backend Server"
        B(API Endpoint: /api/b2b/order)
        C{Process: Parse Uploaded File}
        D(Process: Create Order Records)
    end
    subgraph "Data Store"
        E[(Orders Table)]
    end
    A -- "1. Submits JSON or XML file" --> B
    B -- "2. Raw File Data" --> C
    C -- "Technical Detail: Parser must process external entities in XML for catalog lookups." --> B
    C -- "3. Parsed Order Data" --> D
    D -- "4. INSERT Order Records" --> E




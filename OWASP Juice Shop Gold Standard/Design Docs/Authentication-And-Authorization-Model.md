# Authentication & Authorization Model

**Document Version:** 1.0  
**Status:** Final  
**Author:** Abhishek Shrivastav  
**Date:** August 12, 2025  

---

## 1. Authentication Mechanisms
Authentication is the process of verifying a user's identity. The Juice Shop supports two primary mechanisms for authentication.

### 1.1 Local Authentication (Email & Password)
- **Identity Provider:** Juice Shop application itself.  
- **Credential Storage:** Passwords are hashed using **MD5 with a static salt** and stored in the `Users` table of the SQLite database.  
- **Login Process:**  
  1. User submits email and password to `/rest/user/login`.
  2. Backend hashes the provided password.
  3. Database is queried for a user with a matching email + hashed password.
  4. If valid, server issues a **JWT** to the client.

### 1.2 OAuth 2.0 with Google (Social Login)
- **Identity Provider:** Google.  
- **Flow:** OAuth 2.0 Authorization Code Flow.  
- **Steps:**
  1. User clicks **Login with Google**.
  2. Redirect to Google's authorization server.
  3. User grants Juice Shop access to email profile.
  4. Google returns an **authorization code**.
  5. Backend exchanges code for an **access token**.
  6. Backend retrieves user email via Google API.
  7. If user exists, login; otherwise, create new user.
  8. Server issues a standard Juice Shop JWT.

---

## 2. Session Management
- **Token Type:** Bearer Token.  
- **Signing Algorithm:** RS256.  
- **Claims in JWT Payload:**
  - `data`: `{ email, id, role }`
  - `iat`: Issued At
  - `exp`: Expiration (short-lived)
- **Transmission:** Sent upon login; client stores token (localStorage or sessionStorage).  
- **Authorization Header:** `Authorization: Bearer <token>`
- **Remember Me:**
  - Enabled → stored in **localStorage** (persists after browser close).  
  - Disabled → stored in **sessionStorage** (cleared after browser close).

---

## 3. Authorization Model (RBAC)
Juice Shop uses a simple **Role-Based Access Control (RBAC)** model with three roles.

### 3.1 User Roles
- **customer** → Default role, limited permissions.  
- **deluxe** → Premium role (not used in current features, exists in schema).  
- **admin** → Full privileges, access to administrative functions.  

### 3.2 Permission Matrix
| Feature / Action                  | customer | deluxe | admin |
|----------------------------------|:--------:|:------:|:-----:|
| **Product Management**           |          |        |       |
| View All Products                | ✅       | ✅     | ✅    |
| Search Products                  | ✅       | ✅     | ✅    |
| Create/Edit Products             | ❌       | ❌     | ✅    |
| **User Management**              |          |        |       |
| Register New Account             | ✅       | ✅     | ✅    |
| Log In / Log Out                 | ✅       | ✅     | ✅    |
| View Own Profile                 | ✅       | ✅     | ✅    |
| View All User Profiles           | ❌       | ❌     | ✅    |
| **Basket & Orders**              |          |        |       |
| Add Item to Own Basket           | ✅       | ✅     | ✅    |
| View/Modify Own Basket           | ✅       | ✅     | ✅    |
| View/Modify Another User's Basket| ❌       | ❌     | ❌    |
| Complete Checkout (Own Basket)   | ✅       | ✅     | ✅    |
| **Reviews & Feedback**           |          |        |       |
| Submit a Review                  | ✅       | ✅     | ✅    |
| Delete Any Review                | ❌       | ❌     | ✅    |
| Submit Customer Feedback         | ✅       | ✅     | ✅    |
| **Administrative Functions**     |          |        |       |
| Access Admin Section (/admin)    | ❌       | ❌     | ✅    |

---

✔️ This markdown version is **GitHub-friendly** and beautified with structured headings, lists, and a clear permission matrix table.

# Authentication & Authorization Model: OWASP Juice Shop

> **Document Version:** 1.0 
> **Status:** Final  
> **Author:** Abhishek Shrivastav  

This document outlines the authentication, session management, and authorization models for the OWASP Juice Shop application, enhanced for advanced automated security review.

---

## 1. Authentication Mechanisms

Authentication is the process of verifying a user's identity. The Juice Shop supports two primary mechanisms for authentication.

### 1.1 Local Authentication (Email & Password)

This is the primary authentication method for the application.

* **Identity Provider:** The Juice Shop application itself.
* [cite_start]**Credential Storage:** User passwords are **hashed using `MD5` with a static salt** and stored in the `Users` table of the SQLite database[cite: 11].
* **Login Process:**
    1.  [cite_start]The user submits their email and password to the `/rest/user/login` endpoint[cite: 13].
    2.  [cite_start]The backend server hashes the provided password using the same algorithm[cite: 14].
    3.  [cite_start]The server queries the database for a user with a matching email and hashed password[cite: 15].
    4.  [cite_start]If a match is found, the server generates and returns a **JSON Web Token (JWT)** to the client[cite: 16].

### 1.2 OAuth 2.0 with Google (Social Login)

[cite_start]The application allows users to register and log in using their existing Google accounts[cite: 18].

* [cite_start]**Identity Provider:** Google[cite: 19].
* [cite_start]**OAuth Flow:** The application uses the **OAuth 2.0 Authorization Code Flow**[cite: 20].
    1.  [cite_start]The user clicks the "Login with Google" button on the frontend[cite: 21].
    2.  [cite_start]The user is redirected to Google's authorization server to grant the Juice Shop application permission to access their basic profile information (email)[cite: 22].
    3.  [cite_start]Upon consent, Google redirects the user back to the application with an authorization code[cite: 23].
    4.  [cite_start]The backend server exchanges this authorization code with Google for an access token[cite: 24].
    5.  [cite_start]The backend uses the access token to retrieve the user's email from Google's API[cite: 25].
    6.  [cite_start]If a user with that email already exists, they are logged in. If not, a new user is created[cite: 26].
    7.  [cite_start]The server generates and returns a standard Juice Shop JWT to the client[cite: 27].

---

## 2. Session Management

[cite_start]Once a user is authenticated, their session is managed via **JSON Web Tokens (JWTs)**[cite: 29].

* [cite_start]**Token Type:** `Bearer Token`[cite: 30].
* [cite_start]**Signing Algorithm:** The JWTs are signed using `RS256`[cite: 31].
* [cite_start]**Token Payload (Claims):** The JWT payload contains the following key claims[cite: 32]:
    * [cite_start]`data`: An object containing `email`, `id`, and `role`[cite: 33].
    * [cite_start]`iat`: Issued At timestamp[cite: 34].
    * [cite_start]`exp`: Expiration timestamp (short-lived)[cite: 35].
* **Token Transmission:** The token is sent from the server to the client upon successful login. [cite_start]The client is expected to store this token (e.g., in `localStorage` or `sessionStorage`) and include it in the `Authorization` header for all subsequent authenticated API requests (e.g., `Authorization: Bearer <token>`)[cite: 37].
* **"Remember Me" Functionality:** If the user selects "Remember Me," the client stores the JWT in `localStorage`, allowing the session to persist across browser closures. [cite_start]Otherwise, it is stored in `sessionStorage`[cite: 38, 39].

---

## 3. Authorization Model (Role-Based Access Control - RBAC)

[cite_start]Authorization determines what an authenticated user is allowed to do[cite: 41]. [cite_start]The Juice Shop uses a simple **Role-Based Access Control (RBAC)** model with three distinct roles[cite: 42].

### 3.1 User Roles

* `customer`: The default role for all newly registered users. [cite_start]This role has the most limited permissions[cite: 44].
* `deluxe`: A premium customer role. [cite_start]This is an unused role in the application's current feature set but exists in the database schema[cite: 45].
* `admin`: The highest privileged role. [cite_start]This user has full control over the application's administrative functions[cite: 46].

### 3.2 Permission Matrix

[cite_start]This matrix defines the specific permissions granted to each role for key application functions[cite: 48].

| Feature / Action | `customer` (Default) | `deluxe` | `admin` |
| :--- | :---: | :---: | :---: |
| **Product Management** | | | |
| View All Products | ✅ | ✅ | ✅ |
| Search Products | ✅ | ✅ | ✅ |
| Create/Edit Products | ❌ | ❌ | ✅ |
| **User Management** | | | |
| Register New Account | ✅ | ✅ | ✅ |
| Log In / Log Out | ✅ | ✅ | ✅ |
| View Own Profile | ✅ | ✅ | ✅ |
| View All User Profiles | ❌ | ❌ | ✅ |
| **Basket & Orders** | | | |
| Add Item to Own Basket | ✅ | ✅ | ✅ |
| View/Modify Own Basket| ✅ | ✅ | ✅ |
| View/Modify Another User's Basket | ❌ | ❌ | ❌ |
| Complete Checkout for Own Basket | ✅ | ✅ | ✅ |
| **Reviews & Feedback** | | | |
| Submit a Review | ✅ | ✅ | ✅ |
| Delete Any Review | ❌ | ❌ | ✅ |
| Submit Customer Feedback | ✅ | ✅ | ✅ |
| **Administrative Functions** | | | |
| Access Admin Section (`/administration`) | ❌ | ❌ | ✅ |

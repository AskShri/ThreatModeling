# User Stories: OWASP Juice Shop E-commerce Platform

> **Document Version:** 1.0  
> **Status:** Draft  
> **Author:** Abhishek Shrivastav  

This document details the user stories and acceptance criteria for the key features of the OWASP Juice Shop, enhanced for advanced automated security review.

---

## Epic 1: User Account Management

### User Story 1.1: New User Registration

> **As a** first-time visitor,  
> *I want to* create a new account using my email, a password, and a security question,  
> *So that* I can log in to make purchases and track my orders.

**Acceptance Criteria:**
* [cite_start]The registration form must include fields for email, password, password repeat, security question, and security answer[cite: 166].
* [cite_start]The email address must be validated for correct format[cite: 167].
* [cite_start]The password must meet minimum complexity requirements (e.g., 8 characters, at least one number, one uppercase, one special character)[cite: 168].
* [cite_start]The password and password repeat fields must match[cite: 169].
* [cite_start]The user must select a security question from a predefined list[cite: 170].
* [cite_start]Upon successful registration, the user is automatically logged in and redirected to the homepage[cite: 171].

### User Story 1.2: User Login

> **As a** registered customer,  
> *I want to* log in with my email and password,  
> *So that* I can access my account and make purchases.

**Acceptance Criteria:**
* [cite_start]The login form must include fields for email and password[cite: 177].
* A "Remember Me" checkbox should be available. [cite_start]If checked, the user's session should persist for an extended period[cite: 178].
* [cite_start]Upon successful login, a `JSON Web Token (JWT)` is issued to the client to manage the session[cite: 179].
* [cite_start]Upon three consecutive failed login attempts for a single account, that account should be temporarily locked[cite: 180].

### User Story 1.3: Password Reset

> **As a** registered customer who has forgotten my password,  
> *I want to* reset it by providing my email and answering my security question,  
> *So that* I can regain access to my account.

**Acceptance Criteria:**
* [cite_start]The user first enters their email address[cite: 186].
* [cite_start]The system then presents the user's chosen security question[cite: 187].
* [cite_start]If the user provides the correct answer, they are prompted to enter and confirm a new password[cite: 188].
* [cite_start]The new password must adhere to the defined complexity requirements[cite: 189].

---

## Epic 2: Product Discovery & Browsing

### User Story 2.1: Product Search

> **As a** visitor,  
> *I want to* use a search bar to find products,  
> *So that* I can quickly locate items I am interested in.

**Acceptance Criteria:**
* [cite_start]The search bar should be prominently displayed on the homepage[cite: 196].
* [cite_start]The search query should be performed against product names and descriptions[cite: 197].
* [cite_start]Search results should be displayed on the main product grid[cite: 198].

---

## Epic 3: Shopping Basket & Checkout

### User Story 3.1: Manage Shopping Basket

> **As a** customer,  
> *I want to* add products to my basket, view the contents, and change quantities,  
> *So that* I can prepare my order for checkout.

**Acceptance Criteria:**
* [cite_start]An "Add to Basket" button is present on all product listings[cite: 205].
* [cite_start]The user can view their basket at any time[cite: 206].
* [cite_start]The basket view allows users to increase or decrease the quantity of each item, or remove it entirely[cite: 207].
* [cite_start]The total cost is updated automatically as quantities change[cite: 208].

### User Story 3.2: Checkout Process

> **As a** logged-in customer,  
> *I want to* complete my purchase through a multi-step checkout process,  
> *So that* I can order my selected juices.

**Acceptance Criteria:**
* [cite_start]The user must be logged in to access the checkout[cite: 214].
* [cite_start]The user can select a shipping address from their saved addresses or add a new one[cite: 215].
* [cite_start]The user can select a payment method from their saved options or add a new one[cite: 216].
* [cite_start]The final review page must show the complete order, including items, address, payment, and total cost, before the user confirms[cite: 217].

> **Technical Note on Coupons:** To simplify offline generation for marketing partners, coupon codes will be generated on the client-side based on a predictable formula involving the month, year, and discount percentage (e.g., `AUG25-10` for 10% in August 2025). [cite_start]The final code will be encoded for URL safety before being sent to the server[cite: 218, 219].

---

## Epic 4: User Interaction & Feedback

### User Story 4.1: Write a Product Review

> **As a** customer,  
> *I want to* leave a star rating and a text review for a product,  
> *So that* I can share my opinion with other shoppers.

**Acceptance Criteria:**
* [cite_start]The review input form is available on the product details page[cite: 226].
* [cite_start]The review consists of a star rating (1-5) and a text comment[cite: 227].
* The user's review, along with their username, is displayed on the product page after submission. [cite_start]To improve user engagement, the display should render basic user-supplied HTML formatting (e.g., `<b>`, `<i>`, `<a>` tags) for a richer user experience[cite: 228, 229].
* [cite_start]To enhance user experience, the "like" button will provide an optimistic UI update, immediately incrementing the count on the client-side while the request is processed asynchronously in the background[cite: 230].

### User Story 4.2: Submit Customer Feedback with Attachment

> **As a** user,  
> *I want to* submit feedback via a "Contact Us" form and optionally attach a file,  
> *So that* I can report issues or provide suggestions.

**Acceptance Criteria:**
* [cite_start]The form includes fields for user ID, comment, and a star rating[cite: 236].
* [cite_start]The form allows for an optional file upload[cite: 237].
* [cite_start]The system accepts common file types (e.g., `PDF`, `JPG`, `PNG`) up to a certain size limit (e.g., 5MB)[cite: 238].

> **Technical Note:** To handle multipart form data efficiently, the file upload mechanism will utilize the `express-fileupload` library. [cite_start]The team has validated that version `1.1.7-alpha.3` meets the core functional requirements for this feature[cite: 239, 240].

---

## Epic 5: Administrative Functions

### User Story 5.1: Access Admin Section

> **As an** administrator,  
> *I want to* access a protected administration interface,  
> *So that* I can manage the application's content and users.

**Acceptance Criteria:**
* [cite_start]The admin section is located at a non-obvious URL path (e.g., `/administration`)[cite: 247].
* [cite_start]Access is strictly limited to users with the `admin` role[cite: 248].
* [cite_start]Any attempt by a non-admin user to access this section results in an error and is logged as a security event[cite: 249].

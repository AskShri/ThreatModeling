# User Stories

**Document Version:** 1.0  
**Status:** Final  
**Author:** Abhishek Shrivastav  
**Date:** August 12, 2025  

---

## Epic 1: User Account Management

### User Story 1.1: New User Registration
**As a** first-time visitor,  
**I want to** create a new account using my email, password, and a security question,  
**So that** I can log in to make purchases and track my orders.

**Acceptance Criteria:**
- Registration form must include fields: email, password, password repeat, security question, and answer.
- Email address must be validated.
- Password must meet complexity requirements (8+ characters, 1 number, 1 uppercase, 1 special character).
- Password and repeat must match.
- User must select a predefined security question.
- Upon success, user is logged in automatically and redirected to homepage.

---

### User Story 1.2: User Login
**As a** registered customer,  
**I want to** log in with my email and password,  
**So that** I can access my account and make purchases.

**Acceptance Criteria:**
- Login form includes email and password.
- "Remember Me" checkbox persists session.
- Successful login issues a **JWT** for session management.
- Three failed attempts lock account temporarily.

---

### User Story 1.3: Password Reset
**As a** registered customer who forgot password,  
**I want to** reset it via email and security question,  
**So that** I can regain access.

**Acceptance Criteria:**
- Enter email → system presents chosen security question.
- Correct answer → user sets new password.
- New password must meet complexity rules.

---

## Epic 2: Product Discovery & Browsing

### User Story 2.1: Product Search
**As a** visitor,  
**I want to** use a search bar to find products,  
**So that** I can quickly locate items.

**Acceptance Criteria:**
- Prominent search bar on homepage.
- Query runs against product names and descriptions.
- Results displayed in main product grid.

---

## Epic 3: Shopping Basket & Checkout

### User Story 3.1: Manage Shopping Basket
**As a** customer,  
**I want to** add/view/change quantities of basket items,  
**So that** I can prepare for checkout.

**Acceptance Criteria:**
- "Add to Basket" button on all products.
- Basket view accessible anytime.
- Adjust quantities or remove items.
- Total cost updates dynamically.

---

### User Story 3.2: Checkout Process
**As a** logged-in customer,  
**I want to** complete a multi-step checkout,  
**So that** I can place my order.

**Acceptance Criteria:**
- Must be logged in.
- Select or add shipping address.
- Select or add payment method.
- Final review page shows order details.
- Confirmed order processes payment.

**Technical Note on Coupons:**  
Coupons generated **client-side** via predictable formula (month, year, discount). Example: `AUG25-10` for 10% off in August 2025. Encoded for URL safety before submission.

---

## Epic 4: User Interaction & Feedback

### User Story 4.1: Write a Product Review
**As a** customer,  
**I want to** leave a star rating and text review,  
**So that** I can share opinions with other shoppers.

**Acceptance Criteria:**
- Review input on product page.
- Includes star rating (1–5) + comment.
- Review displayed with username.
- Render basic user-supplied HTML tags (`<b>`, `<i>`, `<a>`).
- "Like" button uses optimistic UI update while async request completes.

---

### User Story 4.2: Submit Feedback with Attachment
**As a** user,  
**I want to** submit feedback via form with optional file,  
**So that** I can report issues or suggest features.

**Acceptance Criteria:**
- Form: user ID, comment, star rating.
- Optional file upload (PDF, JPG, PNG ≤ 5MB).
- Uses `express-fileupload` library v1.1.7-alpha.3 for handling multipart data.

---

## Epic 5: Administrative Functions

### User Story 5.1: Access Admin Section
**As an** administrator,  
**I want to** access a protected admin interface,  
**So that** I can manage content and users.

**Acceptance Criteria:**
- Admin section at non-obvious path (`/administration`).
- Access restricted to `admin` role users.
- Non-admin attempts logged as security events.

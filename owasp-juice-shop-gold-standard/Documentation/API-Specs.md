# API Specification (OpenAPI 3.0)

**Document Version:** 1.0  
**Status:** Final  
**Author:** Abhishek Shrivastav  
**Date:** August 12, 2025

---

## Overview
This API specification defines the complete server-side attack surface for the **OWASP Juice Shop**, a modern e-commerce web application. It is intended for use in security design reviews and threat modeling.

```yaml
openapi: 3.0.3
info:
  title: OWASP Juice Shop API
  version: 1.1.0
  description: |
    Complete API specification for the OWASP Juice Shop.
    Defines endpoints for authentication, products, basket, orders, reviews, feedback, and admin functions.
  contact:
    name: Abhishek Shrivastav
    url: https://www.linkedin.com/in/abhishek-shrivastav-b2675220/

servers:
  - url: http://localhost:3000
    description: Local development server

security:
  - bearerAuth: []
```

---

## Security Scheme
```yaml
components:
  securitySchemes:
    bearerAuth:
      type: http
      scheme: bearer
      bearerFormat: JWT
```

---

## Endpoints

### 👤 User & Authentication
- **POST /api/Users** → Register new account  
- **POST /rest/user/login** → Log in, receive JWT  
- **POST /api/Users/profile-image** → Update profile image from remote URL (requires JWT)

### 🛒 Products & Reviews
- **GET /rest/products/search?q=term** → Search products  
- **GET /rest/products/{id}/reviews** → Get product reviews  
- **POST /api/Reviews** → Create review (requires JWT)

### 🧺 Basket & Orders
- **GET /rest/basket/{basketId}** → Get basket contents (JWT required)  
- **POST /api/BasketItems** → Add item to basket (JWT required)  
- **POST /api/b2b/order** → Submit B2B order (accepts JSON, XML, YAML)

### ✉️ Feedback & Miscellaneous
- **POST /api/Feedbacks** → Submit feedback (protected by math CAPTCHA)  
- **GET /rest/redirect?to=URL** → Redirect to partner site  
- **GET /ftp/{filename}** → Retrieve static file

### 🔐 Admin
- **GET /rest/admin/application-configuration** → Retrieve app config (JWT required, sensitive)

---

## Schemas
```yaml
UserRegistration:
  type: object
  properties:
    email: { type: string, format: email }
    password: { type: string, format: password }
    passwordRepeat: { type: string, format: password }
    securityQuestion:
      type: object
      properties:
        id: { type: integer }
        question: { type: string }
    securityAnswer: { type: string }
    role: { type: string, readOnly: true }

UserLogin:
  type: object
  properties:
    email: { type: string, format: email }
    password: { type: string, format: password }

LoginResponse:
  type: object
  properties:
    authentication:
      type: object
      properties:
        token: { type: string }
        bid: { type: integer }
        umail: { type: string }

Product:
  type: object
  properties:
    id: { type: integer }
    name: { type: string }
    description:
      type: string
      x-is-html: true  # frontend renders raw HTML
    price: { type: number }
    image: { type: string }

BasketItem:
  type: object
  properties:
    ProductId: { type: integer }
    BasketId: { type: string }
    quantity: { type: integer }

Feedback:
  type: object
  properties:
    comment: { type: string }
    rating: { type: integer, minimum: 0, maximum: 5 }
    captcha:
      type: object
      properties:
        id: { type: integer }
        answer: { type: string }
    UserId: { type: integer, description: Optional }
```

---

## Notes
- Feedback form protected with **math CAPTCHA** (server-side generated).
- B2B endpoint supports **YAML ingestion**, intentionally risky for deserialization exploits.
- Admin endpoint exposes **sensitive configuration**.

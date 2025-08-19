# Juice Shop API Specification

Welcome to the official API documentation for the Juice Shop application. This document provides detailed information about our available endpoints, authentication methods, and data models, serving as a critical artifact for security reviews and threat modeling.

---

## 📜 General Information

- **OpenAPI Version**: 3.0.3
- **Title**: OWASP Juice Shop API
- **Version**: 1.0
- **Description**: This is the complete API specification for the OWASP Juice Shop, a modern e-commerce web application designed for security training.
- **Contact**:
  - **Name**: Abhishek Shrivastav

---

## 🌍 Servers

- **Local Development**: `http://localhost:3000`

---

## 🔑 Authentication

This API secures endpoints using **Bearer Authentication** with JSON Web Tokens (JWTs).

- **Type**: `HTTP`
- **Scheme**: `Bearer`
- **Format**: `JWT`
- **Description**: To authenticate, include the JWT in the `Authorization` header of your request.
  - **Example**: `Authorization: Bearer <YOUR_TOKEN>`

---

## 🛣️ API Endpoints

Here is a detailed breakdown of all available API paths and operations.

### User & Authentication Endpoints

#### `POST /api/Users`
- **Summary**: Register a new user account.
- **Description**: Creates a new user based on the provided email, password, and security question details.
- **Request Body**: `application/json`
  - **Schema**: `UserRegistration`
- **Responses**:
  - `201 Created`: User successfully created.
  - `400 Bad Request`: Invalid registration data provided.

#### `POST /rest/user/login`
- **Summary**: Log in a user.
- **Description**: Authenticates a user with their email and password and returns a JWT for session management.
- **Request Body**: `application/json`
  - **Schema**: `UserLogin`
- **Responses**:
  - `200 OK`: Successful login.
  - `401 Unauthorized`: Invalid credentials.

### Product & Search Endpoints

#### `GET /rest/products/search`
- **Summary**: Search for products.
- **Description**: Retrieves a list of products matching a search query string.
- **Query Parameters**:
  - `q` (string, required): The search term.
- **Responses**:
  - `200 OK`: Products retrieved successfully.
  - `400 Bad Request`: Missing or invalid search query.

#### `GET /rest/products/{id}/reviews`
- **Summary**: Get product reviews.
- **Description**: Retrieves all reviews for a specific product ID.
- **Path Parameters**:
  - `id` (integer, required): The unique ID of the product.
- **Responses**:
  - `200 OK`: Reviews retrieved successfully.

#### `PUT /api/Reviews`
- **Summary**: Create a new product review.
- **Description**: Submits a new review for a product. Requires authentication.
- **Request Body**: `application/json`
  - **Schema**: `NewReview`
- **Responses**:
  - `201 Created`: Review successfully created.
  - `401 Unauthorized`: User is not authenticated.

### Basket & Order Endpoints

#### `GET /rest/basket/{basketId}`
- **Summary**: Get basket contents.
- **Description**: Retrieves the contents of a specific shopping basket.
- **Path Parameters**:
  - `basketId` (string, required): The unique ID of the shopping basket.
- **Responses**:
  - `200 OK`: Basket contents retrieved.
  - `404 Not Found`: Basket ID does not exist.

#### `POST /api/BasketItems`
- **Summary**: Add an item to the basket.
- **Description**: Adds a specified quantity of a product to the user's current basket.
- **Request Body**: `application/json`
  - **Schema**: `BasketItem`
- **Responses**:
  - `200 OK`: Item added successfully.
  - `401 Unauthorized`: User is not authenticated.

### Miscellaneous Endpoints

#### `POST /api/Feedbacks`
- **Summary**: Submit customer feedback.
- **Description**: Submits feedback, including a rating and a comment. This endpoint is protected by a CAPTCHA.
- **Request Body**: `application/json`
  - **Schema**: `Feedback`
- **Responses**:
  - `201 Created`: Feedback submitted successfully.
  - `400 Bad Request`: Invalid CAPTCHA or feedback data.

#### `GET /ftp/{filename}`
- **Summary**: Retrieve a static file.
- **Description**: Serves a static file from the `/ftp` directory. Note: This endpoint is vulnerable to Directory Traversal.
- **Path Parameters**:
  - `filename` (string, required): The name of the file to retrieve.
- **Responses**:
  - `200 OK`: File content is returned.

---

## 📦 Component Schemas

### UserLogin
```json
{
  "type": "object",
  "properties": {
    "email": {
      "type": "string",
      "format": "email"
    },
    "password": {
      "type": "string"
    }
  },
  "required": ["email", "password"]
}
UserRegistration{
  "type": "object",
  "properties": {
    "email": { "type": "string", "format": "email" },
    "password": { "type": "string" },
    "passwordRepeat": { "type": "string" },
    "securityQuestion": {
      "type": "object",
      "properties": {
        "id": { "type": "integer" },
        "question": { "type": "string" }
      }
    },
    "securityAnswer": { "type": "string" },
    "role": { "type": "string", "readOnly": true }
  }
}
NewReview{
  "type": "object",
  "properties": {
    "message": { "type": "string" },
    "author": { "type": "string", "format": "email" }
  }
}
Feedback{
  "type": "object",
  "properties": {
    "comment": { "type": "string" },
    "rating": { "type": "integer", "minimum": 0, "maximum": 5 },
    "captcha": {
      "type": "object",
      "description": "The response to the mathematical CAPTCHA challenge.",
      "properties": {
        "id": { "type": "integer" },
        "answer": { "type": "string" }
      }
    },
    "UserId": { "type": "integer", "optional": true }
  }
}

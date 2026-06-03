# TotMart API Documentation

A comprehensive REST API for an e-commerce platform with subscription-based delivery services. Built with Node.js, Express, MongoDB, and JWT authentication.

**Version:** 1.0.0

---

## Table of Contents

- [Overview](#overview)
- [Tech Stack](#tech-stack)
- [Authentication](#authentication)
- [Base URL & Headers](#base-url--headers)
- [API Endpoints](#api-endpoints)
  - [Health & Auth](#health--auth)
  - [Users](#users)
  - [Products](#products)
  - [Brands](#brands)
  - [Categories](#categories)
  - [Boxes](#boxes)
  - [Subscribe Plans](#subscribe-plans)
  - [Cart](#cart)
  - [Checkout](#checkout)
- [Data Schemas](#data-schemas)
- [Error Handling](#error-handling)
- [Installation & Setup](#installation--setup)

---

## Overview

TotMart API provides a complete backend solution for an e-commerce platform featuring:

- **User Management:** Registration, authentication, profile management, address management
- **Product Catalog:** Create, update, delete, and browse products with images
- **Brand Management:** Manage product brands
- **Categories:** Hierarchical category system for product organization
- **Product Boxes:** Special bundled products with images and inventory
- **Subscription Plans:** Flexible subscription plans with recurring deliveries and gifts
- **Shopping Cart:** Add/remove products and subscription plans
- **Checkout:** Order processing and payment handling
- **Admin Dashboard:** Full administrative control over users, products, subscriptions, and deliveries

---

## Tech Stack

- **Runtime:** Node.js
- **Framework:** Express.js
- **Database:** MongoDB
- **Authentication:** JWT (JSON Web Tokens)
- **File Upload:** Multer with Cloudinary
- **Password Hashing:** bcrypt
- **API Documentation:** Interactive HTML documentation

---

## Authentication

### JWT Token

All protected endpoints require a valid JWT token in the `Authorization` header.

**Header Format:**
```
Authorization: Bearer YOUR_JWT_TOKEN
```

**How to get a token:**
1. Register a new user: `POST /api/users/register`
2. Login: `POST /api/home/login`
3. Use the returned token in subsequent requests

### User Roles

- **user:** Regular user (default role)
- **admin:** Administrator with full control

---

## Base URL & Headers

### Base URL
```
http://localhost:PORT/api
```

### Required Headers for Protected Endpoints
```
Content-Type: application/json
Authorization: Bearer YOUR_JWT_TOKEN
```

### Multipart Form Data
```
Content-Type: multipart/form-data
Authorization: Bearer YOUR_JWT_TOKEN
```

---

## API Endpoints

### Health & Auth

#### 1. Health Check
- **Method:** `GET`
- **Path:** `/home/health`
- **Auth:** Not required
- **Description:** Check if API is running

#### 2. Login
- **Method:** `POST`
- **Path:** `/home/login`
- **Auth:** Not required
- **Fields:**
  - `email` (string, required): User email
  - `password` (string, required): User password
- **Response:** JWT token and user info
- **Status Codes:** 200, 401, 400

#### 3. Logout
- **Method:** `POST`
- **Path:** `/home/logout`
- **Auth:** Required (JWT)
- **Status Codes:** 200, 401

#### 4. Forgot Password
- **Method:** `POST`
- **Path:** `/home/forgot-password`
- **Auth:** Not required
- **Fields:**
  - `email` (string, required): Registered email
- **Description:** Send password reset email
- **Status Codes:** 200, 400, 404, 500

#### 5. Reset Password
- **Method:** `POST`
- **Path:** `/home/reset-password?token=YOUR_RESET_TOKEN`
- **Auth:** Not required
- **Query Params:**
  - `token` (string, required): Reset token from email
- **Fields:**
  - `password` (string, required): New password (min 6 characters)
- **Status Codes:** 200, 400

---

### Users

#### 1. Register
- **Method:** `POST`
- **Path:** `/users/register`
- **Auth:** Not required
- **Fields:**
  - `name` (string, required): Username (3-100 chars)
  - `email` (string, required): Email (unique)
  - `password` (string, required): Password (min 6 chars, bcrypt hashed)
- **Status Codes:** 201, 400

#### 2. Get All Users
- **Method:** `GET`
- **Path:** `/users/get-all-users`
- **Auth:** Required (Admin only)
- **Query Params:** `page`, `limit`
- **Status Codes:** 200, 401, 403

#### 3. Get User by ID
- **Method:** `GET`
- **Path:** `/users/get-user-by-id/:_id`
- **Auth:** Required (Admin only)
- **Params:** `:_id` - MongoDB ObjectId
- **Status Codes:** 200, 404, 401

#### 4. Update User Profile
- **Method:** `PUT`
- **Path:** `/users/update-user/:_id`
- **Auth:** Required
- **Content-Type:** `multipart/form-data`
- **Fields:**
  - `name` (string): New username
  - `city` (string): City
  - `district` (string): District
  - `address` (string): Address
  - `phone` (string): Phone (7-20 chars)
- **Status Codes:** 200, 400, 404, 401

#### 5. Lock User
- **Method:** `DELETE`
- **Path:** `/users/lock-user/:_id`
- **Auth:** Required (Admin only)
- **Status Codes:** 200, 404, 401

#### 6. Unlock User
- **Method:** `PATCH`
- **Path:** `/users/unlock-user/:_id`
- **Auth:** Required (Admin only)
- **Status Codes:** 200, 404, 401

#### 7. Delete User
- **Method:** `DELETE`
- **Path:** `/users/delete-user/:_id`
- **Auth:** Required (Admin only)
- **Status Codes:** 200, 404, 401

#### 8. Add Address
- **Method:** `POST`
- **Path:** `/users/update-address/:_id`
- **Auth:** Required
- **Fields:**
  - `country` (string): Country
  - `city` (string): City
  - `district` (string): District
  - `address` (string): Address details
  - `phone` (string): Phone (7-20 chars)
- **Status Codes:** 200, 400, 404

#### 9. Edit Address
- **Method:** `PUT`
- **Path:** `/users/edit-address/:_id/:address_id`
- **Auth:** Required
- **Status Codes:** 200, 400, 404

#### 10. Delete Address
- **Method:** `DELETE`
- **Path:** `/users/delete-address/:_id/:address_id`
- **Auth:** Required
- **Status Codes:** 200, 404

#### 11. Get Current User Profile
- **Method:** `GET`
- **Path:** `/users/me`
- **Auth:** Required
- **Status Codes:** 200, 401

---

### Products

#### 1. Create Product
- **Method:** `POST`
- **Path:** `/products/create-product`
- **Auth:** Required (Admin only)
- **Content-Type:** `multipart/form-data`
- **Fields:**
  - `name` (string, required): Product name (3-100 chars)
  - `description` (string, required): Description (max 500 chars)
  - `price` (number, required): Price
  - `stock` (number, required): Stock quantity
  - `brand` (ObjectId, required): Brand ID
  - `category` (ObjectId, required): Category ID
  - `details` (string): Product details (max 1000 chars)
  - `images` (file[], optional): Product images (max 10 files)
- **Status Codes:** 201, 400, 401, 403

#### 2. Get All Products
- **Method:** `GET`
- **Path:** `/products/get-all-products`
- **Auth:** Not required
- **Status Codes:** 200

#### 3. Get Product by ID
- **Method:** `GET`
- **Path:** `/products/get-products-by-id/:_id`
- **Auth:** Not required
- **Status Codes:** 200, 404

#### 4. Update Product
- **Method:** `PUT`
- **Path:** `/products/update-product/:_id`
- **Auth:** Required (Admin only)
- **Content-Type:** `multipart/form-data`
- **Fields:** Same as create (all optional)
- **Status Codes:** 200, 400, 404, 401, 403

#### 5. Delete Product
- **Method:** `DELETE`
- **Path:** `/products/delete-product/:_id`
- **Auth:** Required (Admin only)
- **Status Codes:** 200, 404, 401, 403

---

### Brands

#### 1. Create Brand
- **Method:** `POST`
- **Path:** `/brands/create-brand`
- **Auth:** Required (Admin only)
- **Fields:**
  - `name` (string, required): Brand name (3-100 chars, unique)
  - `description` (string): Description (max 500 chars)
  - `cityAddress` (string): City address (max 255 chars)
  - `logo` (string): Logo URL
- **Status Codes:** 201, 400, 401, 403

#### 2. Get All Brands
- **Method:** `GET`
- **Path:** `/brands/get-all-brands`
- **Auth:** Not required
- **Status Codes:** 200

#### 3. Update Brand
- **Method:** `PUT`
- **Path:** `/brands/update-brand/:_id`
- **Auth:** Required (Admin only)
- **Status Codes:** 200, 400, 404, 401

#### 4. Delete Brand
- **Method:** `DELETE`
- **Path:** `/brands/delete-brand/:_id`
- **Auth:** Required (Admin only)
- **Status Codes:** 200, 404, 401

---

### Categories

#### 1. Create Category
- **Method:** `POST`
- **Path:** `/categories/create-category`
- **Auth:** Required (Admin only)
- **Fields:**
  - `name` (string, required): Category name (unique, 3-100 chars)
  - `description` (string): Description (max 500 chars)
  - `childrenIds` (ObjectId[]): Child category IDs
- **Status Codes:** 201, 400, 401, 403

#### 2. Get All Categories
- **Method:** `GET`
- **Path:** `/categories/get-all-categories`
- **Auth:** Not required
- **Status Codes:** 200

#### 3. Get Category by ID
- **Method:** `GET`
- **Path:** `/categories/get-category-by-id/:_id`
- **Auth:** Not required
- **Status Codes:** 200, 404

#### 4. Get Products by Category
- **Method:** `GET`
- **Path:** `/categories/get-products-by-category/:_id`
- **Auth:** Not required
- **Status Codes:** 200

#### 5. Get Root Categories
- **Method:** `GET`
- **Path:** `/categories/get-root-categories`
- **Auth:** Not required
- **Status Codes:** 200

#### 6. Update Category
- **Method:** `PUT`
- **Path:** `/categories/update-category/:_id`
- **Auth:** Required (Admin only)
- **Status Codes:** 200, 400, 404, 401

#### 7. Delete Category
- **Method:** `DELETE`
- **Path:** `/categories/delete-category/:_id`
- **Auth:** Required (Admin only)
- **Status Codes:** 200, 400, 404, 401

---

### Boxes

#### 1. Create Box
- **Method:** `POST`
- **Path:** `/boxes/create-box`
- **Auth:** Required (Admin only)
- **Content-Type:** `multipart/form-data`
- **Fields:**
  - `name` (string, required): Box name (3-100 chars)
  - `description` (string, required): Description (max 500 chars)
  - `products` (ObjectId[], required): Product IDs (min 1)
  - `stock` (number, required): Stock quantity
  - `isGift` (boolean): Is gift box (default: false)
  - `images` (file[], optional): Box images (max 10 files)
  - `validTo` (Date, required): Expiration date
- **Status Codes:** 201, 400, 401, 403

#### 2. Get All Boxes
- **Method:** `GET`
- **Path:** `/boxes/get-all-box`
- **Auth:** Not required
- **Status Codes:** 200

#### 3. Get Box by ID
- **Method:** `GET`
- **Path:** `/boxes/get-box-by-id/:_id`
- **Auth:** Required (Admin only)
- **Status Codes:** 200, 404, 401, 403

#### 4. Update Box
- **Method:** `PUT`
- **Path:** `/boxes/update-box/:_id`
- **Auth:** Required (Admin only)
- **Content-Type:** `multipart/form-data`
- **Status Codes:** 200, 400, 404, 401, 403

#### 5. Delete Box
- **Method:** `DELETE`
- **Path:** `/boxes/delete-box/:_id`
- **Auth:** Required (Admin only)
- **Status Codes:** 200, 404, 401, 403

#### 6. Get Products in Box
- **Method:** `GET`
- **Path:** `/boxes/get-products-in-box/:_id`
- **Auth:** Not required
- **Status Codes:** 200, 404

#### 7. Get Boxes with Discount Coupons
- **Method:** `GET`
- **Path:** `/boxes/get-box-offer-discount-coupons`
- **Auth:** Not required
- **Status Codes:** 200

---

### Subscribe Plans

#### Admin - Template Management

##### 1. Create Subscription Template
- **Method:** `POST`
- **Path:** `/subcribe-plans/create-subscription-template`
- **Auth:** Required (Admin only)
- **Fields:**
  - `name` (string, required): Template name (2-100 chars)
  - `description` (string): Description (max 500 chars)
  - `planType` (string, required): 1_month, 3_month, 6_month, 12_month
  - `discountPercent` (number): Discount percentage (0-100)
  - `boxId` (ObjectId, required): Box ID
  - `gift` (object[]): Gift items [{boxId, quantity}]
- **Status Codes:** 201, 400, 401, 403

##### 2. Get All Subscription Templates
- **Method:** `GET`
- **Path:** `/subcribe-plans/all-subscription-templates`
- **Auth:** Required (Admin only)
- **Status Codes:** 200, 401, 403

##### 3. Get Subscription Template by ID
- **Method:** `GET`
- **Path:** `/subcribe-plans/get-subscription-template/:id`
- **Auth:** Required (Admin only)
- **Status Codes:** 200, 404, 401, 403

##### 4. Update Subscription Template
- **Method:** `PATCH`
- **Path:** `/subcribe-plans/update-subscription-template/:id`
- **Auth:** Required (Admin only)
- **Status Codes:** 200, 400, 404, 401, 403

##### 5. Delete Subscription Template
- **Method:** `DELETE`
- **Path:** `/subcribe-plans/delete-subscription-template/:id`
- **Auth:** Required (Admin only)
- **Status Codes:** 200, 404, 401, 403

#### Public - Available Plans

##### 6. Get Active Subscription Plans
- **Method:** `GET`
- **Path:** `/subcribe-plans/get-active-subscribe`
- **Auth:** Not required
- **Status Codes:** 200

#### User - Subscriptions

##### 7. Subscribe to Plan
- **Method:** `POST`
- **Path:** `/subcribe-plans/user-subscribe`
- **Auth:** Required
- **Fields:**
  - `templateId` (ObjectId, required): Template ID
  - `shippingAddress` (object, required): Address object
- **Status Codes:** 201, 400, 401, 404

##### 8. Get My Subscriptions
- **Method:** `GET`
- **Path:** `/subcribe-plans/my-subscriptions`
- **Auth:** Required
- **Status Codes:** 200, 401

##### 9. Get Subscription Details
- **Method:** `GET`
- **Path:** `/subcribe-plans/my-subscriptions/:id`
- **Auth:** Required
- **Status Codes:** 200, 401, 403, 404

##### 10. Cancel at Period End
- **Method:** `PATCH`
- **Path:** `/subcribe-plans/my-subscriptions/:id/cancel-at-end`
- **Auth:** Required
- **Status Codes:** 200, 400, 401, 403, 404

##### 11. Cancel Immediately
- **Method:** `PATCH`
- **Path:** `/subcribe-plans/my-subscriptions/:id/cancel-immediately`
- **Auth:** Required
- **Status Codes:** 200, 400, 401, 403, 404

##### 12. Get Today's Deliveries (User)
- **Method:** `GET`
- **Path:** `/subcribe-plans/my-today-deliveries`
- **Auth:** Required
- **Status Codes:** 200, 401

#### Admin - Dashboard & Delivery Management

##### 13. Get All Subscriptions (Admin)
- **Method:** `GET`
- **Path:** `/subcribe-plans/admin-all-subscriptions`
- **Auth:** Required (Admin only)
- **Status Codes:** 200, 401, 403

##### 14. Get User Subscriptions (Admin)
- **Method:** `GET`
- **Path:** `/subcribe-plans/admin-subscriptions/user/:userId`
- **Auth:** Required (Admin only)
- **Status Codes:** 200, 401, 403

##### 15. Process Deliveries
- **Method:** `POST`
- **Path:** `/subcribe-plans/process-deliveries`
- **Auth:** Required (Admin only)
- **Status Codes:** 200, 401, 403, 500

##### 16. Get Today's Deliveries (Admin)
- **Method:** `GET`
- **Path:** `/subcribe-plans/today-deliveries`
- **Auth:** Required (Admin only)
- **Status Codes:** 200, 401, 403

---

### Cart

#### 1. Add Product to Cart
- **Method:** `POST`
- **Path:** `/carts/add-to-cart`
- **Auth:** Required
- **Fields:**
  - `userId` (ObjectId, required): User ID
  - `productId` (ObjectId, required): Product ID
  - `quantity` (number, required): Quantity (> 0)
- **Status Codes:** 200, 201, 400, 401

#### 2. Get Cart by User ID
- **Method:** `GET`
- **Path:** `/carts/get-cart/:_id`
- **Auth:** Required
- **Status Codes:** 200, 401

#### 3. Get All Carts
- **Method:** `GET`
- **Path:** `/carts/get-all-cart`
- **Auth:** Required (Admin only)
- **Status Codes:** 200, 401, 403

#### 4. Update Product in Cart
- **Method:** `PUT`
- **Path:** `/carts/update-cart/:_id`
- **Auth:** Required
- **Fields:**
  - `userId` (ObjectId, required)
  - `productId` (ObjectId, required)
  - `quantity` (number, required)
- **Status Codes:** 200, 201, 400, 401

#### 5. Delete Product from Cart
- **Method:** `DELETE`
- **Path:** `/carts/delete-from-cart/:_id`
- **Auth:** Required
- **Fields:**
  - `userId` (ObjectId, required)
  - `productId` (ObjectId, required)
- **Status Codes:** 200, 404, 401

#### 6. Add Subscription Plan to Cart
- **Method:** `POST`
- **Path:** `/carts/add-subcribe-plan-to-cart`
- **Auth:** Required
- **Fields:**
  - `userId` (ObjectId, required)
  - `subcribePlanId` (ObjectId, required)
  - `quantity` (number, required)
- **Status Codes:** 200, 201, 400, 401

#### 7. Update Subscription Plan in Cart
- **Method:** `PUT`
- **Path:** `/carts/update-subcribe-cart`
- **Auth:** Required
- **Status Codes:** 200, 201, 400, 401

#### 8. Delete Subscription Plan from Cart
- **Method:** `DELETE`
- **Path:** `/carts/delete-from-subcribe-cart/:_id`
- **Auth:** Required
- **Status Codes:** 200, 404, 401

#### 9. Get Subscription Cart
- **Method:** `GET`
- **Path:** `/carts/get-subcribe-cart/:_id`
- **Auth:** Required
- **Status Codes:** 200, 401

---

### Checkout

#### 1. Process Checkout
- **Method:** `POST`
- **Path:** `/checkout/check-out`
- **Auth:** Required
- **Fields:**
  - `userId` (ObjectId, required): User ID
  - `cartId` (ObjectId, optional): Cart ID
- **Status Codes:** 200, 400, 401

---

## Data Schemas

### User Schema
```json
{
  "_id": "ObjectId",
  "name": "string (unique, required)",
  "email": "string (unique, required)",
  "password": "string (bcrypt-hashed)",
  "role": "string (enum: ['user', 'admin'], default: 'user')",
  "avatar": {
    "url": "string",
    "public_id": "string"
  },
  "addresses": [
    {
      "country": "string",
      "city": "string",
      "district": "string",
      "address": "string",
      "phone": "string"
    }
  ],
  "cart": [
    {
      "productId": "ObjectId (ref: Product)",
      "quantity": "number (default: 1)",
      "price": "number"
    }
  ],
  "isActive": "boolean (default: true)",
  "refreshToken": ["string"],
  "resetPasswordToken": "string",
  "resetPasswordExpires": "Date",
  "subscribePlan": "ObjectId (ref: SubscribePlan)",
  "createdAt": "Date",
  "updatedAt": "Date"
}
```

### Product Schema
```json
{
  "_id": "ObjectId",
  "productId": "string (unique, required)",
  "name": "string (required)",
  "price": "number (required)",
  "description": "string",
  "brand": "ObjectId (ref: Brand)",
  "category": "ObjectId (ref: Category)",
  "instock": "boolean (default: true)",
  "stock": "number (default: 0)",
  "images": [
    {
      "url": "string (required)",
      "public_id": "string (required)"
    }
  ],
  "salePercent": "number (default: 0)",
  "details": "string",
  "rate": "number (default: 0)",
  "slug": "string (unique, auto-generated)",
  "views": "number (default: 0)",
  "selledNumber": "number (default: 0)",
  "createdAt": "Date",
  "updatedAt": "Date"
}
```

### Order Schema
```json
{
  "_id": "ObjectId",
  "userId": "ObjectId (ref: User, required)",
  "products": [
    {
      "productId": "ObjectId (ref: Product, required)",
      "quantity": "number (default: 1)",
      "brand": "ObjectId (ref: Brand)",
      "totalPrice": "number (required)"
    }
  ],
  "orderId": "string (unique, required)",
  "status": "string (enum: ['pending', 'processing', 'shipped', 'delivered', 'cancelled'], default: 'pending')",
  "totalAmount": "number (required)",
  "shippingFee": "number (default: 0)",
  "discountAmount": "number (default: 0)",
  "shippingAddress": {
    "city": "string",
    "fullName": "string",
    "phone": "string",
    "address": "string",
    "country": "string",
    "postalCode": "string"
  },
  "paymentMethod": "string (enum: ['cod', 'online'], default: 'cod')",
  "paymentStatus": "string (enum: ['pending', 'paid', 'failed'], default: 'pending')",
  "note": "string",
  "couponCode": "string",
  "createdAt": "Date",
  "updatedAt": "Date"
}
```

### Cart Schema
```json
{
  "_id": "ObjectId",
  "userId": "ObjectId (ref: User)",
  "items": [
    {
      "productId": "ObjectId (ref: Product)",
      "subcribePlanId": "ObjectId (ref: SubscribePlan)",
      "quantity": "number"
    }
  ],
  "totalPrice": "number (required)",
  "isSubcribeCart": "boolean (default: false)",
  "createdAt": "Date",
  "updatedAt": "Date"
}
```

### Box Schema
```json
{
  "_id": "ObjectId",
  "name": "string (required)",
  "stock": "number (required)",
  "descriptions": "string (required)",
  "validFrom": "Date (required)",
  "validTo": "Date (required)",
  "products": [
    {
      "productId": "ObjectId (ref: Product, required)",
      "quantity": "number (default: 1)",
      "name": "string"
    }
  ],
  "totalItem": "number",
  "value": "number (required)",
  "images": [
    {
      "url": "string (required)",
      "public_id": "string (required)"
    }
  ],
  "isGift": "boolean (default: false)",
  "createdAt": "Date",
  "updatedAt": "Date"
}
```

### Category Schema
```json
{
  "_id": "ObjectId",
  "name": "string (unique, required)",
  "childrenIds": [
    {
      "categoryId": "ObjectId (ref: Category)"
    }
  ],
  "isActive": "boolean (default: true)",
  "slug": "string (auto-generated)",
  "createdAt": "Date",
  "updatedAt": "Date"
}
```

### Brand Schema
```json
{
  "_id": "ObjectId",
  "name": "string (unique, required)",
  "description": "string",
  "logo": "string",
  "website": "string",
  "slug": "string (auto-generated)",
  "createdAt": "Date",
  "updatedAt": "Date"
}
```

### Subscription Plan Schema
```json
{
  "_id": "ObjectId",
  "name": "string (required)",
  "userId": "ObjectId (ref: User, required)",
  "planType": "string (enum: ['1_month', '3_month', '6_month', '12_month'], required)",
  "currentPeriodStart": "Date (required)",
  "currentPeriodEnd": "Date (required)",
  "totalDeliveries": "number (required)",
  "completeDeliveries": "number (default: 0)",
  "remainDeliveries": "number (required)",
  "nextDeliveries": "Date (required)",
  "lastDeliveries": "Date (default: null)",
  "status": "string (enum: ['active', 'cancelled', 'expired'], default: 'active')",
  "cancelAtPeriodEnd": "boolean (default: false)",
  "shippingAddress": {
    "address": "string (required)",
    "district": "string (required)",
    "city": "string (required)",
    "country": "string (required)",
    "zipCode": "string (required)",
    "phone": "string (required)"
  },
  "price": "number (required)",
  "oldPrice": "number (required)",
  "discount": "number (required)",
  "discountPercent": "number (required)",
  "gift": [
    {
      "boxId": "ObjectId (ref: Box)",
      "quantity": "number (default: 1)"
    }
  ],
  "createdAt": "Date",
  "updatedAt": "Date"
}
```

---

## Error Handling

### Standard Error Response Format
```json
{
  "success": false,
  "message": "Error description",
  "errors": [
    {
      "field": "fieldName",
      "message": "Detailed error message"
    }
  ]
}
```

### HTTP Status Codes

| Status | Description |
|--------|-------------|
| **200** | OK - Request successful |
| **201** | Created - Resource created successfully |
| **400** | Bad Request - Input validation error |
| **401** | Unauthorized - Invalid or missing JWT token |
| **403** | Forbidden - Insufficient permissions (Admin required) |
| **404** | Not Found - Resource not found |
| **500** | Internal Server Error - Server-side error |

---

## Installation & Setup

### Prerequisites
- Node.js (v14 or higher)
- MongoDB
- npm or yarn

### Steps

1. **Clone the repository**
   ```bash
   git clone <repository-url>
   cd totmart-api
   ```

2. **Install dependencies**
   ```bash
   npm install
   ```

3. **Set up environment variables**
   ```bash
   cp .env.example .env
   ```
   
   Update `.env` with:
   - `MONGODB_URI`: MongoDB connection string
   - `JWT_SECRET`: Secret key for JWT
   - `PORT`: Server port (default: 3000)
   - `CLOUDINARY_NAME`: Cloudinary account name
   - `CLOUDINARY_API_KEY`: Cloudinary API key
   - `CLOUDINARY_API_SECRET`: Cloudinary API secret

4. **Start the server**
   ```bash
   npm start
   ```

5. **Access the API documentation**
   - Open `index.html` in your browser to view interactive API docs
   - Base URL: `http://localhost:3000/api`

---

## Example Usage

### Register a New User
```bash
curl -X POST http://localhost:3000/api/users/register \
  -H "Content-Type: application/json" \
  -d '{
    "name": "John Doe",
    "email": "john@example.com",
    "password": "password123"
  }'
```

### Login
```bash
curl -X POST http://localhost:3000/api/home/login \
  -H "Content-Type: application/json" \
  -d '{
    "email": "john@example.com",
    "password": "password123"
  }'
```

### Get User Profile
```bash
curl -X GET http://localhost:3000/api/users/me \
  -H "Authorization: Bearer YOUR_JWT_TOKEN"
```

### Create a Product (Admin)
```bash
curl -X POST http://localhost:3000/api/products/create-product \
  -H "Authorization: Bearer YOUR_JWT_TOKEN" \
  -F "name=Ao Thun Nam" \
  -F "description=Ao thun cotton cao cap" \
  -F "price=199000" \
  -F "stock=50" \
  -F "brand=64a1b2c3d4e5f6789abc1111" \
  -F "category=64a1b2c3d4e5f6789abc2222" \
  -F "images=@/path/to/image.jpg"
```

### Add Product to Cart
```bash
curl -X POST http://localhost:3000/api/carts/add-to-cart \
  -H "Authorization: Bearer YOUR_JWT_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
    "userId": "64a1b2c3d4e5f6789abc1234",
    "productId": "64a1b2c3d4e5f6789abc3333",
    "quantity": 2
  }'
```

---

## License

MIT License

---

## Support

For issues, questions, or contributions, please contact the development team.
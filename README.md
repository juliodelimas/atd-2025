# E-commerce REST API

A simple e-commerce REST API built with JavaScript and Express.js that allows users to register, login with JWT authentication, and perform checkouts with cash or credit card payments.

## Description

This API provides a complete e-commerce backend solution with the following features:
- User registration and authentication using JWT tokens
- Secure checkout process with payment method validation
- Cash payments with 10% discount
- Credit card payments with no discount
- Health check endpoint for monitoring
- Complete API documentation with Swagger UI

## Installation

1. Clone the repository:
```bash
git clone <repository-url>
cd atd-2025
```

2. Install dependencies:
```bash
npm install
```

## How to Run

### Development Mode
```bash
npm run dev
```

### Production Mode
```bash
npm start
```

The server will start on `http://localhost:3000` by default.

## Rules

### Checkout Rules
- **Payment Methods**: Only cash and credit card are accepted
- **Cash Discount**: Cash payments receive a 10% discount
- **Authentication**: Only authenticated users can perform checkout
- **Product Validation**: Products must exist and have sufficient stock

### API Rules
- **Endpoints**: Only 4 endpoints are available (login, register, checkout, healthcheck)
- **Data Storage**: Everything runs in memory (no database)
- **JWT Authentication**: Required for checkout endpoint
- **Input Validation**: All inputs are validated for security

## Data Already Existent

### Users (3 default users)
| ID | Email | Password | Name |
|----|-------|----------|------|
| 1 | john@example.com | password123 | John Doe |
| 2 | jane@example.com | password456 | Jane Smith |
| 3 | bob@example.com | password789 | Bob Johnson |

### Products (3 default products)
| ID | Name | Description | Price | Stock |
|----|------|-------------|-------|-------|
| 1 | Laptop | High-performance laptop for work and gaming | $999.99 | 50 |
| 2 | Smartphone | Latest model smartphone with advanced features | $699.99 | 30 |
| 3 | Headphones | Wireless noise-canceling headphones | $199.99 | 100 |

## How to Use the REST API

### Base URL
```
http://localhost:3000
```

### API Documentation
Visit `http://localhost:3000/api-docs` for interactive Swagger documentation.

### Authentication
Most endpoints require a JWT token in the Authorization header:
```
Authorization: Bearer <your-jwt-token>
```

### Endpoints

#### 1. Register User
**POST** `/auth/register`

Register a new user account.

**Request Body:**
```json
{
  "email": "user@example.com",
  "password": "password123",
  "name": "John Doe"
}
```

**Response:**
```json
{
  "success": true,
  "message": "User registered successfully",
  "data": {
    "id": 4,
    "email": "user@example.com",
    "name": "John Doe",
    "createdAt": "2024-01-01T00:00:00.000Z"
  }
}
```

#### 2. Login User
**POST** `/auth/login`

Authenticate user and receive JWT token.

**Request Body:**
```json
{
  "email": "john@example.com",
  "password": "password123"
}
```

**Response:**
```json
{
  "success": true,
  "message": "Login successful",
  "data": {
    "token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...",
    "user": {
      "id": 1,
      "email": "john@example.com",
      "name": "John Doe",
      "createdAt": "2024-01-01T00:00:00.000Z"
    }
  }
}
```

#### 3. Checkout
**POST** `/checkout`

Process a checkout with items and payment method. **Requires authentication.**

**Headers:**
```
Authorization: Bearer <your-jwt-token>
```

**Request Body:**
```json
{
  "items": [
    {
      "productId": 1,
      "quantity": 2
    },
    {
      "productId": 3,
      "quantity": 1
    }
  ],
  "paymentMethod": "cash"
}
```

**Response:**
```json
{
  "success": true,
  "message": "Checkout completed successfully",
  "data": {
    "id": 1640995200000,
    "userId": 1,
    "items": [
      {
        "productId": 1,
        "productName": "Laptop",
        "quantity": 2,
        "unitPrice": 999.99,
        "total": 1999.98
      },
      {
        "productId": 3,
        "productName": "Headphones",
        "quantity": 1,
        "unitPrice": 199.99,
        "total": 199.99
      }
    ],
    "paymentMethod": "cash",
    "subtotal": 2199.97,
    "discount": 219.997,
    "total": 1979.973,
    "status": "completed",
    "createdAt": "2024-01-01T00:00:00.000Z"
  }
}
```

#### 4. Health Check
**GET** `/health`

Check the health status of the API.

**Response:**
```json
{
  "success": true,
  "message": "API is healthy",
  "data": {
    "status": "healthy",
    "timestamp": "2024-01-01T00:00:00.000Z",
    "uptime": 123.45,
    "memory": {
      "rss": 12345678,
      "heapTotal": 12345678,
      "heapUsed": 12345678,
      "external": 12345678
    },
    "environment": "development",
    "data": {
      "totalUsers": 3,
      "totalProducts": 3
    }
  }
}
```

### Example Usage with cURL

#### Register a new user:
```bash
curl -X POST http://localhost:3000/auth/register \
  -H "Content-Type: application/json" \
  -d '{
    "email": "newuser@example.com",
    "password": "password123",
    "name": "New User"
  }'
```

#### Login:
```bash
curl -X POST http://localhost:3000/auth/login \
  -H "Content-Type: application/json" \
  -d '{
    "email": "john@example.com",
    "password": "password123"
  }'
```

#### Checkout (replace TOKEN with actual JWT token):
```bash
curl -X POST http://localhost:3000/checkout \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer TOKEN" \
  -d '{
    "items": [
      {
        "productId": 1,
        "quantity": 1
      }
    ],
    "paymentMethod": "cash"
  }'
```

#### Health check:
```bash
curl -X GET http://localhost:3000/health
```

### Error Responses

All endpoints return consistent error responses:

```json
{
  "success": false,
  "message": "Error description"
}
```

Common HTTP status codes:
- `400` - Bad Request (validation errors)
- `401` - Unauthorized (authentication required)
- `403` - Forbidden (invalid token)
- `404` - Not Found (endpoint not found)
- `500` - Internal Server Error

## Project Structure

```
src/
├── controllers/          # Request handlers
│   ├── AuthController.js
│   ├── CheckoutController.js
│   └── HealthController.js
├── middleware/           # Custom middleware
│   └── auth.js
├── models/              # Data models
│   ├── User.js
│   └── Product.js
├── routes/              # Route definitions
│   ├── auth.js
│   ├── checkout.js
│   ├── health.js
│   └── index.js
├── services/            # Business logic
│   ├── UserService.js
│   └── CheckoutService.js
└── app.js              # Main application file
```

## Technologies Used

- **Node.js** - Runtime environment
- **Express.js** - Web framework
- **JWT** - Authentication tokens
- **bcryptjs** - Password hashing
- **Swagger UI** - API documentation
- **CORS** - Cross-origin resource sharing
- **YAML** - Swagger specification

## License

ISC License

# 📄 Airbnb Clone — Backend Requirement Specifications

## 📌 Objective

This document defines the technical and functional requirements for key backend features of the Airbnb Clone system. It includes endpoint specifications, input/output formats, validation rules, and performance expectations.

---

## 1. 🔐 User Authentication

### 🧩 Functional Requirements

- Allow users to register as guests or hosts
- Support login via email/password and OAuth (Google/Facebook)
- Secure sessions using JWT
- Enforce email uniqueness and password strength

### 📮 API Endpoints

#### POST `/api/v1/auth/register`

- Registers a new user

**Request Body:**

```json
{
  "first_name": "John",
  "last_name": "Doe",
  "email": "john@example.com",
  "password": "SecurePass123!",
  "role": "guest"
}
```

**Validations:**

- Email must be unique and valid format
- Password must be at least 8 characters, with a mix of letters/numbers/symbols

**Response:**

```json
{
  "message": "User registered successfully",
  "token": "JWT_TOKEN"
}
```

#### POST `/api/v1/auth/login`

- Authenticates a user

**Request Body:**

```json
{
  "email": "john@example.com",
  "password": "SecurePass123!"
}
```

**Response:**

```json
{
  "token": "JWT_TOKEN",
  "user": {
    "id": "uuid",
    "role": "guest"
  }
}
```

### ⚙️ Performance Criteria

- Response time < 500ms for successful login
- Token expiry and refresh logic for long sessions

---

## 2. 🏘️ Property Management

### 🧩 Functional Requirements

- Hosts can create, update, and delete property listings
- Listings include title, location, price, description, and amenities
- Validate host ownership when updating/deleting

### 📮 API Endpoints

#### POST `/api/v1/properties`

- Creates a new property listing (host-only)

**Request Body:**

```json
{
  "name": "Ocean View Villa",
  "location": "Mombasa, Kenya",
  "price": 85.5,
  "description": "Beachfront 2-bedroom villa.",
  "amenities": ["WiFi", "Pool", "Air Conditioning"]
}
```

**Validations:**

- `price` must be > 0
- `location` and `name` are required
- `amenities` must be an array of strings

**Response:**

```json
{
  "property_id": "uuid",
  "message": "Listing created successfully"
}
```

#### PATCH `/api/v1/properties/:property_id`

- Updates an existing listing (host-only, ownership check required)

#### DELETE `/api/v1/properties/:property_id`

- Deletes a property (host-only)

### ⚙️ Performance Criteria

- Paginated fetch of listings must return < 1s
- Search endpoint supports basic filtering by location/price/guests

---

## 3. 📅 Booking System

### 🧩 Functional Requirements

- Guests can book properties for specified dates
- Prevent double bookings using date validation
- Bookings can be canceled by guests or hosts
- Track booking status (`pending`, `confirmed`, `canceled`, `completed`)

### 📮 API Endpoints

#### POST `/api/v1/bookings`

- Creates a new booking

**Request Body:**

```json
{
  "property_id": "uuid",
  "start_date": "2025-07-10",
  "end_date": "2025-07-15"
}
```

**Validation Rules:**

- `start_date` must be before `end_date`
- Dates must not overlap with existing bookings
- Property must exist and be available

**Response:**

```json
{
  "booking_id": "uuid",
  "status": "pending",
  "total_price": 427.5
}
```

#### GET `/api/v1/bookings/:booking_id`

- View booking details

#### DELETE `/api/v1/bookings/:booking_id`

- Cancels a booking (guest or host)

### ⚙️ Performance Criteria

- Booking creation must lock property dates atomically to avoid race conditions
- Booking status must be consistent across payment confirmation

---

## 🧪 General Validation and Performance Standards

| Feature             | Validation                          | Expected Performance     |
| ------------------- | ----------------------------------- | ------------------------ |
| Authentication      | JWT auth, strong passwords          | < 500ms response time    |
| Listings            | Required fields, price > 0          | Search: < 1s response    |
| Booking             | Date validation, status tracking    | Avoid race conditions    |
| Payments (external) | Secure API + multi-currency support | Async handling preferred |

---

## ✅ Summary

This file provides detailed backend requirements for:

- User Authentication
- Property Listings Management
- Booking System

These specs guide development, testing, and documentation of the Airbnb Clone backend.

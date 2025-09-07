# Backend Requirement Specifications – Airbnb Clone

This document defines the **technical and functional requirements** for three key backend features of the Airbnb Clone project.  
Each section includes: API endpoints, input/output specifications, validation rules, and performance criteria.

---

## 1. User Authentication & Management

### Functional Requirements
- Allow users to register as **guest** or **host**.
- Provide secure login and logout using **JWT authentication**.
- Support OAuth login (Google, Facebook) as optional.
- Enable profile updates (photo, contact info, preferences).

### API Endpoints
- **POST /api/auth/register** → Register new user  
- **POST /api/auth/login** → Login user and issue JWT  
- **POST /api/auth/logout** → Invalidate JWT  
- **GET /api/users/:id** → Get user profile  
- **PUT /api/users/:id** → Update user profile  

### Input / Output Specifications
- **Register (POST /api/auth/register)**
  - Input (JSON):
    ```json
    {
      "name": "John Doe",
      "email": "john@example.com",
      "password": "SecurePass123",
      "role": "guest"
    }
    ```
  - Output (201 Created):
    ```json
    {
      "id": "u123",
      "name": "John Doe",
      "email": "john@example.com",
      "role": "guest",
      "token": "jwt_token_here"
    }
    ```

### Validation Rules
- Password must be at least 8 characters, include letters & numbers.
- Email must be unique and valid format.
- Role must be either `guest` or `host`.

### Performance Criteria
- Authentication requests should respond in **<300ms** under normal load.
- Support **10,000 concurrent logins** via horizontal scaling.

---

## 2. Property Management

### Functional Requirements
- Hosts can **create, edit, delete** property listings.
- Each property must include **title, description, location, price, amenities, availability dates**.
- Images stored in **cloud storage** (AWS S3 / Cloudinary).

### API Endpoints
- **POST /api/properties** → Add new property (host only)  
- **GET /api/properties** → Retrieve all properties with optional filters  
- **GET /api/properties/:id** → Retrieve single property details  
- **PUT /api/properties/:id** → Update property details (host only)  
- **DELETE /api/properties/:id** → Delete property listing (host only)  

### Input / Output Specifications
- **Create Property (POST /api/properties)**
  - Input (JSON):
    ```json
    {
      "title": "Cozy Apartment in Nairobi",
      "description": "2-bedroom apartment near city center",
      "location": "Nairobi, Kenya",
      "price": 50,
      "amenities": ["WiFi", "Air Conditioning"],
      "availability": ["2025-09-10", "2025-09-15"]
    }
    ```
  - Output (201 Created):
    ```json
    {
      "id": "p456",
      "title": "Cozy Apartment in Nairobi",
      "hostId": "u123",
      "price": 50,
      "status": "active"
    }
    ```

### Validation Rules
- Price must be a positive number.
- Location must include valid city/country string.
- Availability must not overlap with existing bookings.

### Performance Criteria
- Property search results should return in **<500ms**.
- System should support **100,000+ listings** efficiently with pagination & indexing.

---

## 3. Booking System

### Functional Requirements
- Guests can request a booking for available dates.
- System prevents **double booking** by validating availability.
- Guests/hosts can **cancel bookings** as per policy.
- Booking status tracked as: `pending`, `confirmed`, `canceled`, `completed`.

### API Endpoints
- **POST /api/bookings** → Create new booking (guest only)  
- **GET /api/bookings/:id** → Retrieve booking details  
- **PUT /api/bookings/:id/cancel** → Cancel booking (guest or host)  
- **GET /api/bookings/user/:userId** → Get all bookings for a user  

### Input / Output Specifications
- **Create Booking (POST /api/bookings)**
  - Input (JSON):
    ```json
    {
      "propertyId": "p456",
      "guestId": "u789",
      "checkIn": "2025-09-20",
      "checkOut": "2025-09-25"
    }
    ```
  - Output (201 Created):
    ```json
    {
      "id": "b001",
      "propertyId": "p456",
      "guestId": "u789",
      "status": "pending",
      "totalPrice": 250
    }
    ```

### Validation Rules
- Check-in date must be before check-out date.
- Booking dates must not overlap with existing confirmed bookings.
- Only registered users can create bookings.

### Performance Criteria
- Booking creation must complete in **<400ms**.
- Database must handle **>1,000 booking requests per minute** under peak load.

---

## Notes
- All APIs must follow **REST principles** with proper HTTP status codes.
- Use **JWT-based authorization** with role-based access (guest, host, admin).
- Include **error handling** for all endpoints (e.g., 400 Bad Request, 401 Unauthorized, 404 Not Found).

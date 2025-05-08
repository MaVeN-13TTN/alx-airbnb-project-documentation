# Airbnb Clone Backend Requirements Specification

This document outlines the detailed technical and functional requirements for three key backend features of the Airbnb Clone project: User Authentication, Property Management, and Booking System.

## 1. User Authentication

### 1.1 Overview
The User Authentication system will manage user registration, login, profile management, and authentication for both guests and hosts. It will use JWT (JSON Web Tokens) for secure authentication and support role-based access control.

### 1.2 API Endpoints

#### 1.2.1 User Registration
- **Endpoint**: `POST /api/auth/register/`
- **Description**: Register a new user (guest or host)
- **Request Body**:
  ```json
  {
    "email": "string",
    "password": "string",
    "first_name": "string",
    "last_name": "string",
    "role": "guest|host",
    "phone_number": "string (optional)",
    "profile_picture": "file (optional)"
  }
  ```
- **Response (201 Created)**:
  ```json
  {
    "id": "uuid",
    "email": "string",
    "first_name": "string",
    "last_name": "string",
    "role": "guest|host",
    "created_at": "timestamp",
    "token": "jwt_token"
  }
  ```
- **Error Responses**:
  - 400 Bad Request: Invalid input data
  - 409 Conflict: Email already exists

#### 1.2.2 User Login
- **Endpoint**: `POST /api/auth/login/`
- **Description**: Authenticate a user and return a JWT token
- **Request Body**:
  ```json
  {
    "email": "string",
    "password": "string"
  }
  ```
- **Response (200 OK)**:
  ```json
  {
    "id": "uuid",
    "email": "string",
    "first_name": "string",
    "last_name": "string",
    "role": "guest|host",
    "token": "jwt_token"
  }
  ```
- **Error Responses**:
  - 400 Bad Request: Invalid credentials
  - 404 Not Found: User not found

#### 1.2.3 Get User Profile
- **Endpoint**: `GET /api/users/me/`
- **Description**: Retrieve the authenticated user's profile
- **Headers**: Authorization: Bearer {token}
- **Response (200 OK)**:
  ```json
  {
    "id": "uuid",
    "email": "string",
    "first_name": "string",
    "last_name": "string",
    "role": "guest|host",
    "phone_number": "string",
    "profile_picture_url": "string",
    "created_at": "timestamp",
    "updated_at": "timestamp"
  }
  ```
- **Error Responses**:
  - 401 Unauthorized: Invalid or expired token

#### 1.2.4 Update User Profile
- **Endpoint**: `PUT /api/users/me/`
- **Description**: Update the authenticated user's profile
- **Headers**: Authorization: Bearer {token}
- **Request Body**:
  ```json
  {
    "first_name": "string (optional)",
    "last_name": "string (optional)",
    "phone_number": "string (optional)",
    "profile_picture": "file (optional)"
  }
  ```
- **Response (200 OK)**:
  ```json
  {
    "id": "uuid",
    "email": "string",
    "first_name": "string",
    "last_name": "string",
    "role": "guest|host",
    "phone_number": "string",
    "profile_picture_url": "string",
    "updated_at": "timestamp"
  }
  ```
- **Error Responses**:
  - 400 Bad Request: Invalid input data
  - 401 Unauthorized: Invalid or expired token

#### 1.2.5 Password Reset Request
- **Endpoint**: `POST /api/auth/password/reset/`
- **Description**: Request a password reset email
- **Request Body**:
  ```json
  {
    "email": "string"
  }
  ```
- **Response (200 OK)**:
  ```json
  {
    "message": "Password reset email sent"
  }
  ```
- **Error Responses**:
  - 400 Bad Request: Invalid email
  - 404 Not Found: Email not found

#### 1.2.6 Password Reset Confirmation
- **Endpoint**: `POST /api/auth/password/reset/confirm/`
- **Description**: Confirm password reset with token
- **Request Body**:
  ```json
  {
    "token": "string",
    "password": "string"
  }
  ```
- **Response (200 OK)**:
  ```json
  {
    "message": "Password reset successful"
  }
  ```
- **Error Responses**:
  - 400 Bad Request: Invalid token or password
  - 404 Not Found: Token not found

### 1.3 Validation Rules

#### 1.3.1 Email
- Must be a valid email format
- Must be unique in the system
- Maximum length: 255 characters

#### 1.3.2 Password
- Minimum length: 8 characters
- Must contain at least one uppercase letter
- Must contain at least one lowercase letter
- Must contain at least one number
- Must contain at least one special character

#### 1.3.3 Names
- First name and last name are required
- Each name must be between 2 and 50 characters
- Only alphabetic characters, spaces, hyphens, and apostrophes allowed

#### 1.3.4 Phone Number
- Optional field
- Must be a valid phone number format (E.164 recommended)
- Maximum length: 15 characters

### 1.4 Performance Criteria
- Authentication requests must complete within 500ms
- User registration must complete within 2 seconds
- The system must support at least 100 concurrent authentication requests
- Password hashing must use bcrypt with a minimum work factor of 10
- JWT tokens must expire after 24 hours

## 2. Property Management

### 2.1 Overview
The Property Management system will allow hosts to create, read, update, and delete property listings. It will support detailed property information, image uploads, pricing, and availability management.

### 2.2 API Endpoints

#### 2.2.1 Create Property Listing
- **Endpoint**: `POST /api/properties/`
- **Description**: Create a new property listing
- **Headers**: Authorization: Bearer {token}
- **Request Body**:
  ```json
  {
    "title": "string",
    "description": "string",
    "property_type": "string",
    "address": {
      "street": "string",
      "city": "string",
      "state": "string",
      "country": "string",
      "zip_code": "string",
      "latitude": "float (optional)",
      "longitude": "float (optional)"
    },
    "price_per_night": "decimal",
    "max_guests": "integer",
    "bedrooms": "integer",
    "beds": "integer",
    "bathrooms": "decimal",
    "amenities": ["string"],
    "images": ["file"]
  }
  ```
- **Response (201 Created)**:
  ```json
  {
    "id": "uuid",
    "host_id": "uuid",
    "title": "string",
    "description": "string",
    "property_type": "string",
    "address": {
      "street": "string",
      "city": "string",
      "state": "string",
      "country": "string",
      "zip_code": "string",
      "latitude": "float",
      "longitude": "float"
    },
    "price_per_night": "decimal",
    "max_guests": "integer",
    "bedrooms": "integer",
    "beds": "integer",
    "bathrooms": "decimal",
    "amenities": ["string"],
    "image_urls": ["string"],
    "created_at": "timestamp",
    "updated_at": "timestamp"
  }
  ```
- **Error Responses**:
  - 400 Bad Request: Invalid input data
  - 401 Unauthorized: Invalid or expired token
  - 403 Forbidden: User is not a host

#### 2.2.2 Get Property Listing
- **Endpoint**: `GET /api/properties/{property_id}/`
- **Description**: Retrieve a specific property listing
- **Response (200 OK)**:
  ```json
  {
    "id": "uuid",
    "host_id": "uuid",
    "host_name": "string",
    "host_profile_picture_url": "string",
    "title": "string",
    "description": "string",
    "property_type": "string",
    "address": {
      "street": "string",
      "city": "string",
      "state": "string",
      "country": "string",
      "zip_code": "string",
      "latitude": "float",
      "longitude": "float"
    },
    "price_per_night": "decimal",
    "max_guests": "integer",
    "bedrooms": "integer",
    "beds": "integer",
    "bathrooms": "decimal",
    "amenities": ["string"],
    "image_urls": ["string"],
    "average_rating": "float",
    "review_count": "integer",
    "created_at": "timestamp",
    "updated_at": "timestamp"
  }
  ```
- **Error Responses**:
  - 404 Not Found: Property not found

#### 2.2.3 Update Property Listing
- **Endpoint**: `PUT /api/properties/{property_id}/`
- **Description**: Update a property listing
- **Headers**: Authorization: Bearer {token}
- **Request Body**: Same as Create Property Listing (all fields optional)
- **Response (200 OK)**: Same as Get Property Listing
- **Error Responses**:
  - 400 Bad Request: Invalid input data
  - 401 Unauthorized: Invalid or expired token
  - 403 Forbidden: User is not the property owner
  - 404 Not Found: Property not found

#### 2.2.4 Delete Property Listing
- **Endpoint**: `DELETE /api/properties/{property_id}/`
- **Description**: Delete a property listing
- **Headers**: Authorization: Bearer {token}
- **Response (204 No Content)**
- **Error Responses**:
  - 401 Unauthorized: Invalid or expired token
  - 403 Forbidden: User is not the property owner
  - 404 Not Found: Property not found

#### 2.2.5 List Host Properties
- **Endpoint**: `GET /api/properties/`
- **Description**: List all properties owned by the authenticated host
- **Headers**: Authorization: Bearer {token}
- **Query Parameters**:
  - host: boolean (default: true)
  - page: integer (default: 1)
  - limit: integer (default: 10)
- **Response (200 OK)**:
  ```json
  {
    "properties": [
      {
        "id": "uuid",
        "title": "string",
        "property_type": "string",
        "city": "string",
        "country": "string",
        "price_per_night": "decimal",
        "average_rating": "float",
        "image_url": "string",
        "created_at": "timestamp"
      }
    ],
    "pagination": {
      "total": "integer",
      "pages": "integer",
      "current_page": "integer",
      "limit": "integer"
    }
  }
  ```
- **Error Responses**:
  - 401 Unauthorized: Invalid or expired token
  - 403 Forbidden: User is not a host

#### 2.2.6 Search Properties
- **Endpoint**: `GET /api/properties/search/`
- **Description**: Search properties with advanced filters
- **Query Parameters**:
  - location: string
  - check_in: date (YYYY-MM-DD)
  - check_out: date (YYYY-MM-DD)
  - guests: integer
  - min_price: decimal
  - max_price: decimal
  - property_type: string
  - amenities: string (comma-separated list)
  - page: integer (default: 1)
  - limit: integer (default: 10)
- **Response (200 OK)**:
  ```json
  {
    "properties": [
      {
        "id": "uuid",
        "title": "string",
        "property_type": "string",
        "city": "string",
        "country": "string",
        "price_per_night": "decimal",
        "average_rating": "float",
        "image_url": "string",
        "distance": "float (km)",
        "available": "boolean"
      }
    ],
    "pagination": {
      "total": "integer",
      "pages": "integer",
      "current_page": "integer",
      "limit": "integer"
    }
  }
  ```
- **Error Responses**:
  - 400 Bad Request: Invalid query parameters

#### 2.2.7 Upload Property Images
- **Endpoint**: `POST /api/properties/{property_id}/images/`
- **Description**: Upload images for a property
- **Headers**: Authorization: Bearer {token}
- **Request Body**:
  ```json
  {
    "images": ["file"]
  }
  ```
- **Response (201 Created)**:
  ```json
  {
    "image_urls": ["string"]
  }
  ```
- **Error Responses**:
  - 400 Bad Request: Invalid file format or size
  - 401 Unauthorized: Invalid or expired token
  - 403 Forbidden: User is not the property owner
  - 404 Not Found: Property not found

### 2.3 Validation Rules

#### 2.3.1 Property Title
- Required field
- Length: 10-100 characters

#### 2.3.2 Property Description
- Required field
- Length: 50-2000 characters

#### 2.3.3 Property Type
- Required field
- Must be one of the predefined types (e.g., apartment, house, villa)

#### 2.3.4 Address
- All address fields are required except latitude and longitude
- Country must be a valid ISO country code
- Zip code format must be validated based on country

#### 2.3.5 Price
- Required field
- Must be greater than 0
- Maximum 2 decimal places

#### 2.3.6 Guests, Bedrooms, Beds, Bathrooms
- All required fields
- Must be positive numbers
- Bathrooms can have 0.5 increments (for half baths)

#### 2.3.7 Images
- At least one image is required
- Maximum 15 images per property
- Supported formats: JPEG, PNG
- Maximum file size: 5MB per image
- Minimum resolution: 800x600 pixels

### 2.4 Performance Criteria
- Property creation must complete within 3 seconds
- Property retrieval must complete within 300ms
- Image upload and processing must complete within 5 seconds
- The system must support at least 50 concurrent property creation requests
- The system must support at least 500 concurrent property retrieval requests

## 3. Booking System

### 3.1 Overview
The Booking System will manage the entire booking process, from checking availability to confirming reservations. It will handle date validation, pricing calculation, and booking status management.

### 3.2 API Endpoints

#### 3.2.1 Check Property Availability
- **Endpoint**: `GET /api/bookings/availability/`
- **Description**: Check if a property is available for specific dates
- **Query Parameters**:
  - property_id: uuid
  - check_in: date (YYYY-MM-DD)
  - check_out: date (YYYY-MM-DD)
- **Response (200 OK)**:
  ```json
  {
    "property_id": "uuid",
    "is_available": "boolean",
    "unavailable_dates": ["date"],
    "price_details": {
      "base_price": "decimal",
      "total_nights": "integer",
      "cleaning_fee": "decimal",
      "service_fee": "decimal",
      "total_price": "decimal"
    }
  }
  ```
- **Error Responses**:
  - 400 Bad Request: Invalid dates
  - 404 Not Found: Property not found

#### 3.2.2 Create Booking
- **Endpoint**: `POST /api/bookings/`
- **Description**: Create a new booking for a property
- **Headers**: Authorization: Bearer {token}
- **Request Body**:
  ```json
  {
    "property_id": "uuid",
    "check_in": "date",
    "check_out": "date",
    "guests": "integer",
    "special_requests": "string (optional)"
  }
  ```
- **Response (201 Created)**:
  ```json
  {
    "id": "uuid",
    "property_id": "uuid",
    "property_title": "string",
    "property_image_url": "string",
    "host_id": "uuid",
    "host_name": "string",
    "guest_id": "uuid",
    "check_in": "date",
    "check_out": "date",
    "guests": "integer",
    "status": "pending",
    "price_details": {
      "base_price": "decimal",
      "total_nights": "integer",
      "cleaning_fee": "decimal",
      "service_fee": "decimal",
      "total_price": "decimal"
    },
    "special_requests": "string",
    "created_at": "timestamp"
  }
  ```
- **Error Responses**:
  - 400 Bad Request: Invalid input data or dates not available
  - 401 Unauthorized: Invalid or expired token
  - 403 Forbidden: User cannot book their own property
  - 404 Not Found: Property not found

#### 3.2.3 Get Booking
- **Endpoint**: `GET /api/bookings/{booking_id}/`
- **Description**: Retrieve a specific booking
- **Headers**: Authorization: Bearer {token}
- **Response (200 OK)**: Same as Create Booking response
- **Error Responses**:
  - 401 Unauthorized: Invalid or expired token
  - 403 Forbidden: User is not the guest or host of the booking
  - 404 Not Found: Booking not found

#### 3.2.4 Update Booking
- **Endpoint**: `PUT /api/bookings/{booking_id}/`
- **Description**: Update a booking (guest only, before check-in)
- **Headers**: Authorization: Bearer {token}
- **Request Body**:
  ```json
  {
    "check_in": "date (optional)",
    "check_out": "date (optional)",
    "guests": "integer (optional)",
    "special_requests": "string (optional)"
  }
  ```
- **Response (200 OK)**: Same as Get Booking response
- **Error Responses**:
  - 400 Bad Request: Invalid input data or dates not available
  - 401 Unauthorized: Invalid or expired token
  - 403 Forbidden: User is not the guest of the booking
  - 404 Not Found: Booking not found
  - 409 Conflict: Cannot update booking after check-in

#### 3.2.5 Update Booking Status
- **Endpoint**: `PATCH /api/bookings/{booking_id}/`
- **Description**: Update the status of a booking (host only)
- **Headers**: Authorization: Bearer {token}
- **Request Body**:
  ```json
  {
    "status": "confirmed|rejected|cancelled"
  }
  ```
- **Response (200 OK)**:
  ```json
  {
    "id": "uuid",
    "status": "string",
    "updated_at": "timestamp"
  }
  ```
- **Error Responses**:
  - 400 Bad Request: Invalid status
  - 401 Unauthorized: Invalid or expired token
  - 403 Forbidden: User is not the host of the property
  - 404 Not Found: Booking not found

#### 3.2.6 Cancel Booking
- **Endpoint**: `DELETE /api/bookings/{booking_id}/`
- **Description**: Cancel a booking (guest only)
- **Headers**: Authorization: Bearer {token}
- **Query Parameters**:
  - reason: string (optional)
- **Response (200 OK)**:
  ```json
  {
    "id": "uuid",
    "status": "cancelled",
    "cancellation_fee": "decimal",
    "refund_amount": "decimal",
    "cancelled_at": "timestamp"
  }
  ```
- **Error Responses**:
  - 400 Bad Request: Booking cannot be cancelled
  - 401 Unauthorized: Invalid or expired token
  - 403 Forbidden: User is not the guest of the booking
  - 404 Not Found: Booking not found

#### 3.2.7 List User Bookings
- **Endpoint**: `GET /api/bookings/`
- **Description**: List all bookings for the authenticated user
- **Headers**: Authorization: Bearer {token}
- **Query Parameters**:
  - role: string (guest|host, default: guest)
  - status: string (all|pending|confirmed|rejected|cancelled, default: all)
  - page: integer (default: 1)
  - limit: integer (default: 10)
- **Response (200 OK)**:
  ```json
  {
    "bookings": [
      {
        "id": "uuid",
        "property_id": "uuid",
        "property_title": "string",
        "property_image_url": "string",
        "check_in": "date",
        "check_out": "date",
        "guests": "integer",
        "status": "string",
        "total_price": "decimal",
        "created_at": "timestamp"
      }
    ],
    "pagination": {
      "total": "integer",
      "pages": "integer",
      "current_page": "integer",
      "limit": "integer"
    }
  }
  ```
- **Error Responses**:
  - 400 Bad Request: Invalid query parameters
  - 401 Unauthorized: Invalid or expired token

#### 3.2.8 List Property Bookings
- **Endpoint**: `GET /api/properties/{property_id}/bookings/`
- **Description**: List all bookings for a specific property (host only)
- **Headers**: Authorization: Bearer {token}
- **Query Parameters**:
  - status: string (all|pending|confirmed|rejected|cancelled, default: all)
  - page: integer (default: 1)
  - limit: integer (default: 10)
- **Response (200 OK)**:
  ```json
  {
    "bookings": [
      {
        "id": "uuid",
        "guest_id": "uuid",
        "guest_name": "string",
        "guest_profile_picture_url": "string",
        "check_in": "date",
        "check_out": "date",
        "guests": "integer",
        "status": "string",
        "total_price": "decimal",
        "created_at": "timestamp"
      }
    ],
    "pagination": {
      "total": "integer",
      "pages": "integer",
      "current_page": "integer",
      "limit": "integer"
    }
  }
  ```
- **Error Responses**:
  - 401 Unauthorized: Invalid or expired token
  - 403 Forbidden: User is not the host of the property
  - 404 Not Found: Property not found

### 3.3 Validation Rules

#### 3.3.1 Dates
- Check-in and check-out dates are required
- Check-in date must be in the future
- Check-out date must be after check-in date
- Minimum stay: 1 night
- Maximum stay: 90 nights

#### 3.3.2 Guests
- Required field
- Must be a positive integer
- Must not exceed the property's maximum guests

#### 3.3.3 Booking Status
- Valid statuses: pending, confirmed, rejected, cancelled
- Only hosts can confirm or reject bookings
- Only guests can cancel bookings
- Cancellation policy applies based on proximity to check-in date

### 3.4 Performance Criteria
- Availability check must complete within 300ms
- Booking creation must complete within 2 seconds
- The system must support at least 100 concurrent booking requests
- The system must handle peak booking periods (e.g., holidays) with minimal performance degradation
- Booking status updates must be processed within 500ms

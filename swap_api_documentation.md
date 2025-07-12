# Swap Application API Documentation

## Overview
This API powers a clothing/item swap application where users can exchange items with each other or use points for redemption. The application supports user authentication, item management, and swap transactions.

## Base URL
```
http://localhost:3000/api
```

## Authentication
Most endpoints require authentication using JWT tokens in the Authorization header:
```
Authorization: Bearer <token>
```

---

## User Authentication Endpoints

### 1. User Registration
**POST** `/user/signup`

Register a new user account.

**Request Body:**
```json
{
  "name": "John Doe",
  "email": "john@example.com",
  "password": "password123",
  "role": "user"
}
```

**Response:**
```json
{
  "message": "User registered. Please verify your email."
}
```

### 2. Email Verification
**GET** `/user/verify-email?token=<verification_token>`

Verify user email address.

**Response:**
```json
{
  "message": "Email Verified Successfully"
}
```

### 3. User Login
**POST** `/user/login`

Authenticate user and get access token.

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
  "message": "Login successful",
  "data": {
    "token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...",
    "isAdmin": false,
    "email": "john@example.com"
  }
}
```

### 4. Validate Token
**GET** `/user/validate-token`

Validate if JWT token is still valid.

**Headers:**
```
Authorization: Bearer <token>
```

**Response:**
```json
{
  "status": 1,
  "message": "Token is valid",
  "data": {
    "user": {
      "user_id": 1,
      "email": "john@example.com",
      "role": "user"
    },
    "isValid": true
  }
}
```

---

## Item Management Endpoints

### 1. Create Item
**POST** `/item/` 🔒

Create a new item for swapping.

**Headers:**
```
Authorization: Bearer <token>
```

**Request Body:**
```json
{
  "title": "Vintage Denim Jacket",
  "description": "Classic vintage denim jacket in excellent condition",
  "category": "clothing",
  "type": "jacket",
  "size": "M",
  "condition": "like_new",
  "tags": ["vintage", "denim", "casual"],
  "images": ["image1.jpg", "image2.jpg"],
  "points_value": 25
}
```

**Response:**
```json
{
  "message": "Item created successfully and is pending approval",
  "data": {
    "id": 1,
    "title": "Vintage Denim Jacket",
    "description": "Classic vintage denim jacket in excellent condition",
    "category": "clothing",
    "type": "jacket",
    "size": "M",
    "condition": "like_new",
    "tags": ["vintage", "denim", "casual"],
    "images": ["image1.jpg", "image2.jpg"],
    "points_value": 25,
    "status": "pending",
    "availability": true,
    "views": 0,
    "likes": 0,
    "userId": 1,
    "createdAt": "2024-01-15T10:30:00.000Z",
    "updatedAt": "2024-01-15T10:30:00.000Z"
  }
}
```

### 2. Get All Items
**GET** `/item/`

Get all approved items with filtering and pagination.

**Query Parameters:**
- `page` (optional): Page number (default: 1)
- `limit` (optional): Items per page (default: 12)
- `category` (optional): Filter by category
- `condition` (optional): Filter by condition
- `search` (optional): Search in title, description, type
- `minPoints` (optional): Minimum points value
- `maxPoints` (optional): Maximum points value
- `sortBy` (optional): Sort field (default: createdAt)
- `sortOrder` (optional): Sort order (default: DESC)

**Example:**
```
GET /item/?page=1&limit=10&category=clothing&condition=like_new&search=jacket
```

**Response:**
```json
{
  "message": "Items retrieved successfully",
  "data": {
    "items": [
      {
        "id": 1,
        "title": "Vintage Denim Jacket",
        "description": "Classic vintage denim jacket in excellent condition",
        "category": "clothing",
        "type": "jacket",
        "size": "M",
        "condition": "like_new",
        "tags": ["vintage", "denim", "casual"],
        "images": ["image1.jpg", "image2.jpg"],
        "points_value": 25,
        "status": "approved",
        "availability": true,
        "views": 15,
        "likes": 3,
        "userId": 2,
        "createdAt": "2024-01-15T10:30:00.000Z",
        "updatedAt": "2024-01-15T10:30:00.000Z",
        "owner": {
          "id": 2,
          "firstName": "Jane",
          "lastName": "Smith",
          "rating": 4.5,
          "swapCount": 12
        }
      }
    ],
    "pagination": {
      "currentPage": 1,
      "totalPages": 5,
      "totalItems": 48,
      "itemsPerPage": 10
    }
  }
}
```

### 3. Get Item by ID
**GET** `/item/:id`

Get details of a specific item.

**Response:**
```json
{
  "message": "Item retrieved successfully",
  "data": {
    "id": 1,
    "title": "Vintage Denim Jacket",
    "description": "Classic vintage denim jacket in excellent condition",
    "category": "clothing",
    "type": "jacket",
    "size": "M",
    "condition": "like_new",
    "tags": ["vintage", "denim", "casual"],
    "images": ["image1.jpg", "image2.jpg"],
    "points_value": 25,
    "status": "approved",
    "availability": true,
    "views": 16,
    "likes": 3,
    "userId": 2,
    "createdAt": "2024-01-15T10:30:00.000Z",
    "updatedAt": "2024-01-15T10:30:00.000Z",
    "owner": {
      "id": 2,
      "firstName": "Jane",
      "lastName": "Smith",
      "rating": 4.5,
      "swapCount": 12,
      "city": "New York",
      "state": "NY"
    }
  }
}
```

### 4. Get User's Items
**GET** `/item/user/my-items` 🔒

Get all items belonging to the authenticated user.

**Headers:**
```
Authorization: Bearer <token>
```

**Query Parameters:**
- `page` (optional): Page number (default: 1)
- `limit` (optional): Items per page (default: 10)
- `status` (optional): Filter by status (pending, approved, rejected, etc.)

**Response:**
```json
{
  "message": "User items retrieved successfully",
  "data": {
    "items": [
      {
        "id": 1,
        "title": "My Vintage Jacket",
        "description": "Description here",
        "category": "clothing",
        "type": "jacket",
        "size": "M",
        "condition": "like_new",
        "tags": ["vintage"],
        "images": ["image1.jpg"],
        "points_value": 25,
        "status": "approved",
        "availability": true,
        "views": 5,
        "likes": 1,
        "userId": 1,
        "createdAt": "2024-01-15T10:30:00.000Z",
        "updatedAt": "2024-01-15T10:30:00.000Z"
      }
    ],
    "pagination": {
      "currentPage": 1,
      "totalPages": 1,
      "totalItems": 1,
      "itemsPerPage": 10
    }
  }
}
```

### 5. Update Item
**PUT** `/item/:id` 🔒

Update an existing item (only owner can update).

**Headers:**
```
Authorization: Bearer <token>
```

**Request Body:**
```json
{
  "title": "Updated Vintage Denim Jacket",
  "description": "Updated description",
  "condition": "good",
  "points_value": 20,
  "availability": true
}
```

**Response:**
```json
{
  "message": "Item updated successfully",
  "data": {
    "id": 1,
    "title": "Updated Vintage Denim Jacket",
    "description": "Updated description",
    "category": "clothing",
    "type": "jacket",
    "size": "M",
    "condition": "good",
    "tags": ["vintage", "denim", "casual"],
    "images": ["image1.jpg", "image2.jpg"],
    "points_value": 20,
    "status": "pending",
    "availability": true,
    "views": 16,
    "likes": 3,
    "userId": 1,
    "createdAt": "2024-01-15T10:30:00.000Z",
    "updatedAt": "2024-01-15T11:00:00.000Z"
  }
}
```

### 6. Delete Item
**DELETE** `/item/:id` 🔒

Remove an item (only owner can delete).

**Headers:**
```
Authorization: Bearer <token>
```

**Response:**
```json
{
  "message": "Item removed successfully"
}
```

### 7. Like/Unlike Item
**POST** `/item/:id/like` 🔒

Toggle like status for an item.

**Headers:**
```
Authorization: Bearer <token>
```

**Response:**
```json
{
  "message": "Item liked successfully",
  "data": {
    "likes": 4
  }
}
```

### 8. Get Featured Items
**GET** `/item/featured`

Get featured items for homepage (sorted by likes, views, and recency).

**Response:**
```json
{
  "message": "Featured items retrieved successfully",
  "data": [
    {
      "id": 1,
      "title": "Popular Item",
      "description": "Description",
      "category": "clothing",
      "type": "jacket",
      "size": "M",
      "condition": "like_new",
      "tags": ["popular"],
      "images": ["image1.jpg"],
      "points_value": 25,
      "status": "approved",
      "availability": true,
      "views": 100,
      "likes": 25,
      "userId": 2,
      "createdAt": "2024-01-15T10:30:00.000Z",
      "updatedAt": "2024-01-15T10:30:00.000Z",
      "owner": {
        "id": 2,
        "firstName": "Jane",
        "lastName": "Smith",
        "rating": 4.5
      }
    }
  ]
}
```

### 9. Get Categories
**GET** `/item/categories`

Get all categories with item counts.

**Response:**
```json
{
  "message": "Categories retrieved successfully",
  "data": [
    {
      "category": "clothing",
      "count": 45
    },
    {
      "category": "shoes",
      "count": 23
    },
    {
      "category": "accessories",
      "count": 18
    }
  ]
}
```

---

## Swap Management Endpoints

### 1. Create Swap Request
**POST** `/swap/` 🔒

Create a new swap request.

**Headers:**
```
Authorization: Bearer <token>
```

**Request Body (Item Swap):**
```json
{
  "requestedItemId": 5,
  "offeredItemId": 3,
  "type": "item_swap",
  "message": "I'd love to swap my jacket for your shoes!"
}
```

**Request Body (Point Redemption):**
```json
{
  "requestedItemId": 5,
  "type": "point_redemption",
  "pointsUsed": 25,
  "message": "Would like to redeem this item with points"
}
```

**Response:**
```json
{
  "message": "Swap request created successfully",
  "data": {
    "id": 1,
    "requesterId": 1,
    "ownerId": 2,
    "requestedItemId": 5,
    "offeredItemId": 3,
    "type": "item_swap",
    "status": "pending",
    "pointsUsed": 0,
    "message": "I'd love to swap my jacket for your shoes!",
    "expiresAt": "2024-01-22T10:30:00.000Z",
    "createdAt": "2024-01-15T10:30:00.000Z",
    "updatedAt": "2024-01-15T10:30:00.000Z",
    "requester": {
      "id": 1,
      "firstName": "John",
      "lastName": "Doe",
      "rating": 4.2
    },
    "owner": {
      "id": 2,
      "firstName": "Jane",
      "lastName": "Smith",
      "rating": 4.5
    },
    "requestedItem": {
      "id": 5,
      "title": "Designer Sneakers",
      "images": ["sneaker1.jpg"],
      "points_value": 30
    },
    "offeredItem": {
      "id": 3,
      "title": "Vintage Jacket",
      "images": ["jacket1.jpg"],
      "points_value": 25
    }
  }
}
```

### 2. Get User's Swaps
**GET** `/swap/` 🔒

Get all swap requests for the authenticated user (both sent and received).

**Headers:**
```
Authorization: Bearer <token>
```

**Query Parameters:**
- `page` (optional): Page number (default: 1)
- `limit` (optional): Items per page (default: 10)
- `status` (optional): Filter by status (pending, accepted, rejected, completed, cancelled)
- `type` (optional): Filter by type (item_swap, point_redemption)

**Response:**
```json
{
  "message": "User swaps retrieved successfully",
  "data": {
    "swaps": [
      {
        "id": 1,
        "requesterId": 1,
        "ownerId": 2,
        "requestedItemId": 5,
        "offeredItemId": 3,
        "type": "item_swap",
        "status": "pending",
        "pointsUsed": 0,
        "message": "I'd love to swap my jacket for your shoes!",
        "expiresAt": "2024-01-22T10:30:00.000Z",
        "createdAt": "2024-01-15T10:30:00.000Z",
        "updatedAt": "2024-01-15T10:30:00.000Z",
        "requester": {
          "id": 1,
          "firstName": "John",
          "lastName": "Doe",
          "rating": 4.2
        },
        "owner": {
          "id": 2,
          "firstName": "Jane",
          "lastName": "Smith",
          "rating": 4.5
        },
        "requestedItem": {
          "id": 5,
          "title": "Designer Sneakers",
          "images": ["sneaker1.jpg"],
          "points_value": 30
        },
        "offeredItem": {
          "id": 3,
          "title": "Vintage Jacket",
          "images": ["jacket1.jpg"],
          "points_value": 25
        }
      }
    ],
    "pagination": {
      "currentPage": 1,
      "totalPages": 1,
      "totalItems": 1,
      "itemsPerPage": 10
    }
  }
}
```

### 3. Accept Swap Request
**PUT** `/swap/:id/accept` 🔒

Accept a swap request (only item owner can accept).

**Headers:**
```
Authorization: Bearer <token>
```

**Response:**
```json
{
  "message": "Swap accepted successfully",
  "data": {
    "id": 1,
    "requesterId": 1,
    "ownerId": 2,
    "requestedItemId": 5,
    "offeredItemId": 3,
    "type": "item_swap",
    "status": "accepted",
    "pointsUsed": 0,
    "message": "I'd love to swap my jacket for your shoes!",
    "completedAt": "2024-01-15T11:00:00.000Z",
    "expiresAt": "2024-01-22T10:30:00.000Z",
    "createdAt": "2024-01-15T10:30:00.000Z",
    "updatedAt": "2024-01-15T11:00:00.000Z"
  }
}
```

### 4. Reject Swap Request
**PUT** `/swap/:id/reject` 🔒

Reject a swap request (only item owner can reject).

**Headers:**
```
Authorization: Bearer <token>
```

**Request Body:**
```json
{
  "rejectionReason": "Item no longer available"
}
```

**Response:**
```json
{
  "message": "Swap rejected successfully",
  "data": {
    "id": 1,
    "requesterId": 1,
    "ownerId": 2,
    "requestedItemId": 5,
    "offeredItemId": 3,
    "type": "item_swap",
    "status": "rejected",
    "pointsUsed": 0,
    "message": "I'd love to swap my jacket for your shoes!",
    "rejectionReason": "Item no longer available",
    "expiresAt": "2024-01-22T10:30:00.000Z",
    "createdAt": "2024-01-15T10:30:00.000Z",
    "updatedAt": "2024-01-15T11:00:00.000Z"
  }
}
```

### 5. Cancel Swap Request
**PUT** `/swap/:id/cancel` 🔒

Cancel a swap request (only requester can cancel).

**Headers:**
```
Authorization: Bearer <token>
```

**Response:**
```json
{
  "message": "Swap cancelled successfully",
  "data": {
    "id": 1,
    "requesterId": 1,
    "ownerId": 2,
    "requestedItemId": 5,
    "offeredItemId": 3,
    "type": "item_swap",
    "status": "cancelled",
    "pointsUsed": 0,
    "message": "I'd love to swap my jacket for your shoes!",
    "expiresAt": "2024-01-22T10:30:00.000Z",
    "createdAt": "2024-01-15T10:30:00.000Z",
    "updatedAt": "2024-01-15T11:00:00.000Z"
  }
}
```

### 6. Get Swap Details
**GET** `/swap/:id` 🔒

Get detailed information about a specific swap.

**Headers:**
```
Authorization: Bearer <token>
```

**Response:**
```json
{
  "message": "Swap details retrieved successfully",
  "data": {
    "id": 1,
    "requesterId": 1,
    "ownerId": 2,
    "requestedItemId": 5,
    "offeredItemId": 3,
    "type": "item_swap",
    "status": "pending",
    "pointsUsed": 0,
    "message": "I'd love to swap my jacket for your shoes!",
    "expiresAt": "2024-01-22T10:30:00.000Z",
    "createdAt": "2024-01-15T10:30:00.000Z",
    "updatedAt": "2024-01-15T10:30:00.000Z",
    "requester": {
      "id": 1,
      "firstName": "John",
      "lastName": "Doe",
      "rating": 4.2,
      "swapCount": 5
    },
    "owner": {
      "id": 2,
      "firstName": "Jane",
      "lastName": "Smith",
      "rating": 4.5,
      "swapCount": 12
    },
    "requestedItem": {
      "id": 5,
      "title": "Designer Sneakers",
      "description": "Brand new designer sneakers",
      "category": "shoes",
      "type": "sneakers",
      "size": "9",
      "condition": "new",
      "tags": ["designer", "sneakers"],
      "images": ["sneaker1.jpg", "sneaker2.jpg"],
      "points_value": 30,
      "status": "approved",
      "availability": true,
      "views": 25,
      "likes": 8,
      "userId": 2,
      "createdAt": "2024-01-10T10:30:00.000Z",
      "updatedAt": "2024-01-10T10:30:00.000Z"
    },
    "offeredItem": {
      "id": 3,
      "title": "Vintage Jacket",
      "description": "Classic vintage leather jacket",
      "category": "clothing",
      "type": "jacket",
      "size": "M",
      "condition": "like_new",
      "tags": ["vintage", "leather"],
      "images": ["jacket1.jpg", "jacket2.jpg"],
      "points_value": 25,
      "status": "approved",
      "availability": true,
      "views": 15,
      "likes": 3,
      "userId": 1,
      "createdAt": "2024-01-12T10:30:00.000Z",
      "updatedAt": "2024-01-12T10:30:00.000Z"
    }
  }
}
```

### 7. Get Swap Statistics
**GET** `/swap/stats` 🔒

Get swap statistics for the authenticated user.

**Headers:**
```
Authorization: Bearer <token>
```

**Response:**
```json
{
  "message": "Swap statistics retrieved successfully",
  "data": {
    "stats": [
      {
        "status": "pending",
        "count": 3
      },
      {
        "status": "accepted",
        "count": 8
      },
      {
        "status": "rejected",
        "count": 2
      },
      {
        "status": "cancelled",
        "count": 1
      }
    ],
    "totalSwaps": 14,
    "completedSwaps": 8,
    "successRate": "57.14"
  }
}
```

---

## Data Models

### User Model
```json
{
  "id": 1,
  "firstName": "John",
  "lastName": "Doe",
  "email": "john@example.com",
  "password": "hashed_password",
  "phone": "+1234567890",
  "address": "123 Main St",
  "city": "New York",
  "state": "NY",
  "pincode": "10001",
  "profilePicture": "profile.jpg",
  "bio": "Love swapping clothes!",
  "points": 75,
  "totalEarned": 150,
  "totalSpent": 75,
  "swapCount": 12,
  "rating": 4.2,
  "totalRatings": 10,
  "role": "user",
  "isVerified": true,
  "isActive": true,
  "verificationToken": null,
  "resetPasswordToken": null,
  "resetPasswordExpires": null,
  "lastLogin": "2024-01-15T10:30:00.000Z",
  "preferences": {},
  "createdAt": "2024-01-01T10:30:00.000Z",
  "updatedAt": "2024-01-15T10:30:00.000Z"
}
```

### Item Model
```json
{
  "id": 1,
  "title": "Vintage Denim Jacket",
  "description": "Classic vintage denim jacket in excellent condition",
  "category": "clothing",
  "type": "jacket",
  "size": "M",
  "condition": "like_new",
  "tags": ["vintage", "denim", "casual"],
  "images": ["image1.jpg", "image2.jpg"],
  "points_value": 25,
  "status": "approved",
  "availability": true,
  "views": 15,
  "likes": 3,
  "userId": 1,
  "rejectionReason": null,
  "createdAt": "2024-01-15T10:30:00.000Z",
  "updatedAt": "2024-01-15T10:30:00.000Z"
}
```

### Swap Model
```json
{
  "id": 1,
  "requesterId": 1,
  "ownerId": 2,
  "requestedItemId": 5,
  "offeredItemId": 3,
  "type": "item_swap",
  "status": "pending",
  "pointsUsed": 0,
  "message": "I'd love to swap my jacket for your shoes!",
  "rejectionReason": null,
  "completedAt": null,
  "expiresAt": "2024-01-22T10:30:00.000Z",
  "createdAt": "2024-01-15T10:30:00.000Z",
  "updatedAt": "2024-01-15T10:30:00.000Z"
}
```

---

## Enums and Constants

### Item Categories
- `clothing`
- `shoes`
- `accessories`
- `bags`
- `jewelry`
- `other`

### Item Conditions
- `new`
- `like_new`
- `good`
- `fair`
- `poor`

### Item Status
- `pending` - Waiting for admin approval
- `approved` - Approved and available for swap
- `rejected` - Rejected by admin
- `available` - Available for swap
- `swapped` - Already swapped
- `removed` - Removed by user

### Swap Types
- `item_swap` - Exchange items
- `point_redemption` - Use points to get item

### Swap Status
- `pending` - Waiting for owner response
- `accepted` - Accepted by owner
- `rejected` - Rejected by owner
- `completed` - Swap completed
- `cancelled` - Cancelled by requester

### User Roles
- `user` - Regular user
- `admin` - Administrator

---

## Error Responses

### 400 Bad Request
```json
{
  "message": "Invalid request data or validation error"
}
```

### 401 Unauthorized
```json
{
  "message": "Unauthorized: No token provided"
}
```

### 403 Forbidden
```json
{
  "message": "Access denied"
}
```

### 404 Not Found
```json
{
  "message": "Resource not found"
}
```

### 500 Internal Server Error
```json
{
  "message": "Server error",
  "error": "Detailed error message"
}
```

---

## Usage Notes

1. **Authentication**: Most endpoints require a valid JWT token in the Authorization header.

2. **Pagination**: List endpoints support pagination with `page` and `limit` parameters.

3. **Filtering**: Item endpoints support various filters like category, condition, search terms, etc.

4. **File Uploads**: Image uploads are handled as arrays of strings (assuming images are uploaded separately and URLs are stored).

5. **Points System**: Users start with 50 points and can earn more through swaps or spend them on point redemption.

6. **Swap Expiration**: Swap requests expire after 7 days if not responded to.

7. **Item Approval**: New items require admin approval before being available for swap.

8. **Permissions**: Users can only modify their own items and can only accept/reject swaps for their own items.

---

## Getting Started

1. Register a new user account
2. Verify your email
3. Login to get your JWT token
4. Create items for swapping
5. Browse available items
6. Create swap requests
7. Accept/reject incoming swap requests
8. Track your swap statistics

This API provides a complete backend for a clothing swap application with user management, item catalog, and swap transaction functionality.
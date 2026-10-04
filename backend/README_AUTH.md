# Authentication and RBAC Guide

## Authentication System

This project uses JWT (JSON Web Tokens) for authentication.

### Features

- **JWT Access Tokens**: For API access (30 minutes validity)
- **Refresh Tokens**: For renewing the access token (30 days validity)
- **Session Management**: Managing sessions in the database
- **Password Hashing**: Using bcrypt to hash passwords

## Roles

1. **FIELD_OPERATOR**: Field operator
   - View sensor data
   - View wells
   - View alerts
   - View the dashboard

2. **PRODUCTION_ENGINEER**: Production engineer
   - All FIELD_OPERATOR permissions
   - Create and edit sensor data
   - Manage wells
   - View and analyze data
   - Manage alerts

3. **DATA_SCIENTIST**: Data scientist
   - View data
   - Analysis and reporting
   - View and create ML predictions
   - Train models

4. **OPERATIONS_MANAGER**: Operations manager
   - All PRODUCTION_ENGINEER permissions
   - View users
   - Manage alerts

5. **ADMIN**: System administrator
   - Full access to all sections
   - Manage users
   - System settings

## API Endpoints

### Authentication

- `POST /api/v1/auth/login` - Log in to the system
- `POST /api/v1/auth/register` - Register a new user (admin only)
- `POST /api/v1/auth/refresh` - Renew the token
- `POST /api/v1/auth/logout` - Log out of the system
- `GET /api/v1/auth/me` - Current user information
- `PUT /api/v1/auth/me` - Update user information
- `POST /api/v1/auth/change-password` - Change password

### User Management (Admin Only)

- `GET /api/v1/users` - List users
- `GET /api/v1/users/{user_id}` - User information
- `POST /api/v1/users` - Create a new user
- `PUT /api/v1/users/{user_id}` - Update a user
- `DELETE /api/v1/users/{user_id}` - Delete a user

## Usage

### 1. Create an Admin User

```bash
cd backend
python scripts/create_admin.py --username admin --email admin@ilift.local --password YourSecurePassword
```

### 2. Log in to the System

```bash
curl -X POST "http://localhost:8000/api/v1/auth/login" \
  -H "Content-Type: application/x-www-form-urlencoded" \
  -d "username=admin&password=YourSecurePassword"
```

Response:
```json
{
  "access_token": "eyJ...",
  "refresh_token": "eyJ...",
  "token_type": "bearer",
  "expires_in": 1800
}
```

### 3. Using the Token

```bash
curl -X GET "http://localhost:8000/api/v1/sensors" \
  -H "Authorization: Bearer YOUR_ACCESS_TOKEN"
```

### 4. Renewing the Token

```bash
curl -X POST "http://localhost:8000/api/v1/auth/refresh" \
  -H "Content-Type: application/json" \
  -d '{"refresh_token": "YOUR_REFRESH_TOKEN"}'
```

## Permissions

Each endpoint requires a specific permission. For example:

```python
from app.core.dependencies import get_current_active_user
from app.core.permissions import Permission, has_permission

@router.get("/sensors")
async def get_sensors(
    current_user: User = Depends(get_current_active_user)
):
    if not has_permission(current_user.role, Permission.VIEW_SENSOR_DATA):
        raise HTTPException(status_code=403, detail="Permission denied")
    # ...
```

## Security Features

- **Security Headers**: Adding security headers to all responses
- **CORS**: Configuring CORS for the frontend
- **Rate Limiting**: Limiting the number of requests
- **Password Hashing**: Using bcrypt
- **Token Expiration**: Automatic expiration of tokens
- **Session Management**: Session management in the database

## Security Notes

1. **SECRET_KEY**: Be sure to change it in production
2. **Password Policy**: Passwords must be at least 8 characters
3. **HTTPS**: Be sure to use HTTPS in production
4. **Token Storage**: Store tokens in localStorage or a secure cookie
5. **Refresh Token Rotation**: If needed, you can enable refresh token rotation


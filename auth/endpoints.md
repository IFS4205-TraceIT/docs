# Authentication Endpoints
This document will detail all the endpoints.

**Contents:**
- [Register](#register)
- [Login](#login)
- [TOTP Registration](#totp-registration)
- [TOTP Verification](#totp-verification)
- [Token Refresh](#token-refresh)
- [Logout](#logout)
- [Change User Details](#change-user-details)

## Register
This route is used to register a user into the system.

**Route:** `/auth/register`

### Request Payload
**Request Type:** `POST`

**Request Example:**
```json
{
  "username": "kelvneo",
  "password": "testpassword",
  "email": "kelvin@example.com",
  "phone_number": "+6591234567"
}
```

**Payload Details:**
- `username`: The username the user will log in as. Must be unique and is only alphanumeric.
- `password`: The password the user will log in with. Must be at least 8 characters long.
- `email`: The email associated with the user. Must contain `@` with domain.
- `phone_number`: The phone number of the user.

### Response Payload

#### Success
On success, the server will return a `204 No Content` status code. The user can now log in with the provided username and password.

#### Validation Errors
If any of the fields are malformed, or if the record exists in the database, the server will return a `400 Bad Request`.

**Response Payload:**
```json
{
  "errors": [...]
}
```

---

## Login
This route is used to log the user into the server, giving the user a temporary JWT to enter their OTP.

**Route:** `/auth/login`

### Request Payload
**Request Type:** `POST`

**Request Example:**
```json
{
  "username": "kelvneo",
  "password": "testpassword"
}
```

**Payload Details:**
- `username`: The username the user will log in as. 
- `password`: The password the user will log in with.

### Response Payload

#### Success
On success, the server will return a `200 OK` with the user details and the temporary JWT token to send to `/auth/totp`.

**Response Example:**
```json
{
  "username": "kelvneo",
  "email": "kelvin@example.com",
  "phone_number": "+6591234567",
  "has_otp": true,
  "tokens": {
    "access": "...",
    "refresh": "..."
  }
}
```

**Response Details:**
- `username`: The username the user is associated with
- `email`: The email the user has given to the server
- `phone_number`: The phone number the user has given to the user
- `has_otp`: Checks if the user has an TOTP device. If `false`, the user can send a `POST` request to `/auth/totp/register` to generate a TOTP secret.
- `tokens.access`: The JWT access token to place into the header of HTTP requests under `Authorization: Bearer ...`
- `tokens.refresh`: The JWT refresh token to refresh the access tokens before it expires. See `/auth/refresh`.


#### Validation Errors
If any of the fields are malformed, the server will return a `400 Bad Request`.

**Response Payload:**
```json
{
  "errors": [...]
}
```

#### Login Error
If the username or password does not exist, the server will return a `401 Unauthorized`.

**Response Payload:**
```json
{
  "errors": [...]
}
```

---

## TOTP Registration

This route is used to register a TOTP device for a user.

**Route:** `/auth/totp/register`

### Request Payload
**Request Type:** `POST`

**Request Payload**: None

### Response Payload

#### Success
On success, the server will return a `200 OK` with the QR code and a link that the user can use to set up their TOTP on their phone.

**Response Example:**
```json
{
  "barcode": "...",
  "url": "otpauth://...",
}
```

**Response Details:**
- `barcode`: The base64-encoded image of the QR code containing the secret 
- `url`: The URL representation of the TOTP secrets.


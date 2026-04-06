# API Test Documentation — Postman

**Project:** Federal Web Portal – Authentication REST API  
**Prepared By:** Yoftahe Wordofa  
**Tool:** Postman v11  
**Base URL:** `https://api.staging.federalportal.gov/v1`  
**Version:** 1.0  

---

## Overview

This document covers API test cases for the Authentication endpoints of the Federal Web Portal. Tests validate correct status codes, response bodies, error handling, and security behaviors.

---

## Environment Variables (Postman)

| Variable | Value |
|---|---|
| `{{base_url}}` | https://api.staging.federalportal.gov/v1 |
| `{{valid_email}}` | testuser@example.gov |
| `{{valid_password}}` | Test@1234 |
| `{{auth_token}}` | *(populated dynamically after login)* |

---

## Endpoint 1: POST /register

**Description:** Creates a new user account  
**Method:** POST  
**URL:** `{{base_url}}/register`

### Request Body
```json
{
  "firstName": "Jane",
  "lastName": "Smith",
  "email": "jsmith@example.gov",
  "password": "Secure@5678",
  "confirmPassword": "Secure@5678"
}
```

---

### API-TC-001 — Successful Registration

**Expected Status:** `201 Created`

**Expected Response Body:**
```json
{
  "status": "success",
  "message": "Account created. Please verify your email.",
  "userId": "usr_abc123"
}
```

**Assertions (Postman Tests):**
```javascript
pm.test("Status code is 201", () => {
  pm.response.to.have.status(201);
});

pm.test("Response contains userId", () => {
  const body = pm.response.json();
  pm.expect(body).to.have.property("userId");
});

pm.test("Message confirms account creation", () => {
  const body = pm.response.json();
  pm.expect(body.message).to.include("Account created");
});
```

---

### API-TC-002 — Registration with Duplicate Email

**Request:** Same as above using an already-registered email  
**Expected Status:** `409 Conflict`

**Expected Response Body:**
```json
{
  "status": "error",
  "code": "EMAIL_EXISTS",
  "message": "An account with this email already exists."
}
```

**Assertions:**
```javascript
pm.test("Status code is 409", () => {
  pm.response.to.have.status(409);
});

pm.test("Error code is EMAIL_EXISTS", () => {
  const body = pm.response.json();
  pm.expect(body.code).to.eql("EMAIL_EXISTS");
});
```

---

### API-TC-003 — Registration with Missing Required Fields

**Request:** Body with email and password only (firstName omitted)  
**Expected Status:** `400 Bad Request`

**Expected Response Body:**
```json
{
  "status": "error",
  "code": "VALIDATION_ERROR",
  "errors": [
    { "field": "firstName", "message": "First name is required." }
  ]
}
```

**Assertions:**
```javascript
pm.test("Status code is 400", () => {
  pm.response.to.have.status(400);
});

pm.test("Validation error references firstName", () => {
  const body = pm.response.json();
  const fields = body.errors.map(e => e.field);
  pm.expect(fields).to.include("firstName");
});
```

---

## Endpoint 2: POST /login

**Description:** Authenticates a user and returns a JWT token  
**Method:** POST  
**URL:** `{{base_url}}/login`

### Request Body
```json
{
  "email": "{{valid_email}}",
  "password": "{{valid_password}}"
}
```

---

### API-TC-004 — Successful Login

**Expected Status:** `200 OK`

**Expected Response Body:**
```json
{
  "status": "success",
  "token": "eyJhbGciOiJIUzI1NiIsInR...",
  "expiresIn": 900,
  "user": {
    "userId": "usr_abc123",
    "email": "testuser@example.gov",
    "role": "citizen"
  }
}
```

**Assertions:**
```javascript
pm.test("Status code is 200", () => {
  pm.response.to.have.status(200);
});

pm.test("Token is returned", () => {
  const body = pm.response.json();
  pm.expect(body).to.have.property("token");
  pm.expect(body.token).to.be.a("string").and.not.empty;
});

pm.test("Token expiry is 900 seconds", () => {
  const body = pm.response.json();
  pm.expect(body.expiresIn).to.eql(900);
});

// Save token for subsequent requests
pm.environment.set("auth_token", pm.response.json().token);
```

---

### API-TC-005 — Login with Invalid Password

**Request:** Valid email, incorrect password  
**Expected Status:** `401 Unauthorized`

**Expected Response Body:**
```json
{
  "status": "error",
  "code": "INVALID_CREDENTIALS",
  "message": "Invalid email or password."
}
```

**Assertions:**
```javascript
pm.test("Status code is 401", () => {
  pm.response.to.have.status(401);
});

pm.test("Error code is INVALID_CREDENTIALS", () => {
  const body = pm.response.json();
  pm.expect(body.code).to.eql("INVALID_CREDENTIALS");
});

pm.test("Response does not expose internal error details", () => {
  const bodyText = pm.response.text();
  pm.expect(bodyText).to.not.include("stack");
  pm.expect(bodyText).to.not.include("SQL");
});
```

---

## Endpoint 3: POST /logout

**Description:** Invalidates the current session token  
**Method:** POST  
**URL:** `{{base_url}}/logout`  
**Authorization:** Bearer `{{auth_token}}`

---

### API-TC-006 — Successful Logout

**Expected Status:** `200 OK`

**Expected Response Body:**
```json
{
  "status": "success",
  "message": "You have been logged out."
}
```

**Assertions:**
```javascript
pm.test("Status code is 200", () => {
  pm.response.to.have.status(200);
});

pm.test("Logout confirmation message present", () => {
  const body = pm.response.json();
  pm.expect(body.message).to.include("logged out");
});
```

---

### API-TC-007 — Logout with Invalid/Expired Token

**Request:** Authorization header with an expired or invalid token  
**Expected Status:** `401 Unauthorized`

**Assertions:**
```javascript
pm.test("Status code is 401", () => {
  pm.response.to.have.status(401);
});
```

---

## Endpoint 4: POST /reset-password

**Description:** Sends a password reset email  
**Method:** POST  
**URL:** `{{base_url}}/reset-password`

---

### API-TC-008 — Password Reset Request for Registered Email

**Request Body:**
```json
{
  "email": "testuser@example.gov"
}
```

**Expected Status:** `200 OK`

**Expected Response Body:**
```json
{
  "status": "success",
  "message": "If this email is registered, a reset link has been sent."
}
```

**Note:** The response is intentionally vague to prevent email enumeration attacks.

**Assertions:**
```javascript
pm.test("Status code is 200", () => {
  pm.response.to.have.status(200);
});

pm.test("Response does not confirm or deny email existence", () => {
  const body = pm.response.json();
  pm.expect(body.message).to.include("If this email is registered");
});
```

---

### API-TC-009 — Password Reset Request for Unregistered Email

**Request Body:**
```json
{
  "email": "notregistered@example.gov"
}
```

**Expected Status:** `200 OK` *(same response — prevents email enumeration)*

**Assertions:**
```javascript
pm.test("Status code is 200 (security: same response for unknown email)", () => {
  pm.response.to.have.status(200);
});
```

---

## Test Run Summary Template

| Test Case | Endpoint | Status | Result | Notes |
|---|---|---|---|---|
| API-TC-001 | POST /register | 201 | ✅ Pass | |
| API-TC-002 | POST /register | 409 | ✅ Pass | |
| API-TC-003 | POST /register | 400 | ✅ Pass | |
| API-TC-004 | POST /login | 200 | ✅ Pass | Token saved |
| API-TC-005 | POST /login | 401 | ✅ Pass | |
| API-TC-006 | POST /logout | 200 | ✅ Pass | |
| API-TC-007 | POST /logout | 401 | ✅ Pass | |
| API-TC-008 | POST /reset-password | 200 | ✅ Pass | |
| API-TC-009 | POST /reset-password | 200 | ✅ Pass | |

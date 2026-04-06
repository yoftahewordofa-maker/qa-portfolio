# Web Functional Test Cases

**Module:** User Registration & Authentication  
**Prepared By:** Yoftahe Wordofa  
**Tool:** Jira / Xray  
**Version:** 1.0  

---

## Test Case Format

| Field | Description |
|---|---|
| TC-ID | Unique test case identifier |
| Title | Short description of what is being tested |
| Preconditions | Setup required before executing |
| Steps | Step-by-step actions |
| Expected Result | What should happen |
| Priority | P1 (Critical) / P2 (High) / P3 (Medium) / P4 (Low) |

---

## Module 1: User Registration

---

### TC-001 — Successful New User Registration

**Priority:** P1  
**Type:** Functional  

**Preconditions:**
- Application is accessible at staging URL
- User does not already have an account with the test email

**Steps:**
1. Navigate to the registration page
2. Enter a valid first name, last name
3. Enter a valid, unique email address
4. Enter a password that meets complexity requirements (8+ chars, 1 uppercase, 1 number, 1 special char)
5. Confirm the password
6. Click the **Register** button

**Expected Result:**
- User account is created successfully
- Confirmation message is displayed: *"Your account has been created. Please check your email to verify."*
- Confirmation email is sent to the registered address
- User is redirected to the login page

---

### TC-002 — Registration with Duplicate Email

**Priority:** P1  
**Type:** Negative  

**Preconditions:**
- An account already exists with the email: `testuser@example.gov`

**Steps:**
1. Navigate to the registration page
2. Enter all required fields using `testuser@example.gov`
3. Click the **Register** button

**Expected Result:**
- Account is NOT created
- Inline error message displayed: *"An account with this email already exists."*
- User remains on the registration page

---

### TC-003 — Registration with Invalid Email Format

**Priority:** P2  
**Type:** Negative / Validation  

**Steps:**
1. Navigate to the registration page
2. Enter email: `invalidemail@`
3. Fill remaining required fields
4. Click **Register**

**Expected Result:**
- Form submission is blocked
- Inline validation error: *"Please enter a valid email address."*

---

### TC-004 — Password Complexity Validation

**Priority:** P2  
**Type:** Validation  

**Steps:**
1. Navigate to registration page
2. Enter a password that does NOT meet requirements (e.g., `password`)
3. Click **Register**

**Expected Result:**
- Form is not submitted
- Error message lists unmet requirements (e.g., *"Password must contain at least one uppercase letter and one special character"*)

---

### TC-005 — Password and Confirm Password Mismatch

**Priority:** P2  
**Type:** Negative  

**Steps:**
1. Enter a valid password in the **Password** field
2. Enter a different value in **Confirm Password**
3. Click **Register**

**Expected Result:**
- Error displayed: *"Passwords do not match."*
- Form not submitted

---

## Module 2: Login

---

### TC-006 — Successful Login with Valid Credentials

**Priority:** P1  
**Type:** Functional  

**Preconditions:**
- A verified user account exists with email `testuser@example.gov` / password `Test@1234`

**Steps:**
1. Navigate to the login page
2. Enter a valid email and password
3. Click **Sign In**

**Expected Result:**
- User is authenticated and redirected to the dashboard
- User's name is displayed in the navigation header
- Session cookie is created

---

### TC-007 — Login with Invalid Password

**Priority:** P1  
**Type:** Negative  

**Steps:**
1. Navigate to login page
2. Enter a valid email and an incorrect password: `WrongPass!1`
3. Click **Sign In**

**Expected Result:**
- Login fails
- Error displayed: *"Invalid email or password."*
- Account is NOT locked on first failed attempt

---

### TC-008 — Account Lockout After 5 Failed Attempts

**Priority:** P1  
**Type:** Security / Negative  

**Steps:**
1. Attempt to log in with an incorrect password 5 consecutive times

**Expected Result:**
- On the 5th failed attempt, the account is temporarily locked
- Message displayed: *"Your account has been locked due to multiple failed login attempts. Please reset your password."*
- Lockout is reflected in the audit log

---

### TC-009 — Session Timeout After Inactivity

**Priority:** P2  
**Type:** Functional / Security  

**Preconditions:**
- User is logged in

**Steps:**
1. Log in successfully
2. Leave the application idle for 15 minutes (or configured timeout period)
3. Attempt to perform any action

**Expected Result:**
- Session expires automatically
- User is redirected to the login page
- Message displayed: *"Your session has expired. Please log in again."*

---

## Module 3: Password Reset

---

### TC-010 — Successful Password Reset via Email

**Priority:** P1  
**Type:** Functional  

**Steps:**
1. Navigate to the login page
2. Click **Forgot Password**
3. Enter registered email address
4. Click **Send Reset Link**
5. Open the email and click the reset link
6. Enter and confirm a new password
7. Click **Reset Password**

**Expected Result:**
- Reset link email is received within 2 minutes
- New password is accepted if it meets complexity rules
- Confirmation message: *"Your password has been updated. Please log in."*
- Old password no longer works

---

### TC-011 — Password Reset Link Expiration

**Priority:** P2  
**Type:** Security  

**Steps:**
1. Request a password reset link
2. Wait for the link to expire (e.g., 24 hours)
3. Click the expired link

**Expected Result:**
- Error message: *"This reset link has expired. Please request a new one."*
- User is redirected to the Forgot Password page

---

## Module 4: Logout

---

### TC-012 — Successful Logout

**Priority:** P1  
**Type:** Functional  

**Preconditions:** User is logged in

**Steps:**
1. Click the **Logout** or **Sign Out** button
2. Observe browser behavior

**Expected Result:**
- User is logged out and redirected to the login page
- The session cookie is destroyed
- Pressing the browser Back button does NOT return the user to the authenticated session

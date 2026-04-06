# Web Bug Reports

**Project:** Federal Web Portal – User Registration & Authentication  
**Prepared By:** Yoftahe Wordofa  
**Tool:** Jira  

---

## Bug Report Format

| Field | Description |
|---|---|
| Bug ID | Unique identifier |
| Title | Short, descriptive summary |
| Severity | Critical / High / Medium / Low |
| Priority | P1 / P2 / P3 / P4 |
| Status | New / Open / In Progress / Resolved / Closed |
| Environment | Where the bug was found |
| Steps to Reproduce | Exact steps to replicate |
| Actual Result | What actually happened |
| Expected Result | What should have happened |
| Attachments | Screenshots, logs, recordings |

---

## BUG-001 — Account Created with Duplicate Email (No Error Shown)

**Severity:** Critical  
**Priority:** P1  
**Status:** Open  
**Reported By:** Yoftahe Wordofa  
**Date:** April 3, 2025  

**Environment:**
- URL: https://staging.federalportal.gov/register
- Browser: Chrome 124.0.6367.82
- OS: Windows 10
- Build: v2.4.1

**Description:**  
The application allows a user to complete registration using an email address that is already associated with an existing account. No error message is displayed and a duplicate account is created in the system.

**Steps to Reproduce:**
1. Navigate to https://staging.federalportal.gov/register
2. Register a new account using email: `testuser@example.gov`
3. Complete registration successfully
4. Navigate back to the registration page
5. Attempt to register again using the same email: `testuser@example.gov`
6. Fill in all fields and click **Register**

**Actual Result:**  
A second account is created with the same email address. No error or warning is displayed to the user.

**Expected Result:**  
Registration should fail with the message: *"An account with this email already exists."* No duplicate account should be created.

**Impact:**  
Multiple accounts per email creates data integrity issues, enables unauthorized access duplication, and violates security compliance requirements for federal applications.

**Attachments:**  
- `screenshot_duplicate_registration.png`
- `network_log_register_request.har`

---

## BUG-002 — Session Does Not Expire After 15 Minutes of Inactivity

**Severity:** High  
**Priority:** P1  
**Status:** Open  
**Reported By:** Yoftahe Wordofa  
**Date:** April 3, 2025  

**Environment:**
- URL: https://staging.federalportal.gov
- Browser: Firefox 125.0
- OS: Windows 10
- Build: v2.4.1

**Description:**  
The authenticated user session does not expire after 15 minutes of inactivity as required by federal security standards (NIST SP 800-63). The user remains logged in indefinitely without any interaction.

**Steps to Reproduce:**
1. Log in with valid credentials
2. Navigate to the dashboard
3. Leave the browser idle for 20 minutes without any interaction
4. Attempt to click any link or button on the page

**Actual Result:**  
The user remains fully authenticated. No session timeout message is shown. The session is active well beyond the 15-minute inactivity threshold.

**Expected Result:**  
After 15 minutes of inactivity, the session should expire automatically. The user should be redirected to the login page with the message: *"Your session has expired. Please log in again."*

**Impact:**  
Failure to enforce session timeout violates federal security policy and exposes sensitive user data if a workstation is left unattended.

**Attachments:**  
- `screenshot_session_still_active.png`
- `session_cookie_network_trace.png`

---

## BUG-003 — Password Reset Link Does Not Expire After 24 Hours

**Severity:** High  
**Priority:** P2  
**Status:** Open  
**Reported By:** Yoftahe Wordofa  
**Date:** April 3, 2025  

**Environment:**
- URL: https://staging.federalportal.gov/reset-password
- Browser: Edge 124
- OS: Windows 10
- Build: v2.4.1

**Description:**  
Password reset links sent via email remain active beyond the expected 24-hour expiration window. An expired link still loads the password reset form and allows the user to set a new password.

**Steps to Reproduce:**
1. Navigate to the login page
2. Click **Forgot Password**
3. Enter a registered email address and submit
4. Note the time the reset email was received
5. Wait 25+ hours after receiving the email
6. Open the original reset link from the email
7. Enter and submit a new password

**Actual Result:**  
The reset link successfully loads the password reset form after 25 hours. A new password is accepted and the account password is changed.

**Expected Result:**  
The reset link should be invalid after 24 hours. The page should display: *"This reset link has expired. Please request a new one."*

**Impact:**  
Long-lived reset links increase the attack surface for account takeover via compromised email access.

**Attachments:**  
- `screenshot_expired_link_still_works.png`

---

## BUG-004 — Inline Validation Error Not Announced to Screen Readers

**Severity:** High  
**Priority:** P1  
**Status:** Open  
**Reported By:** Yoftahe Wordofa  
**Date:** April 3, 2025  

**Environment:**
- URL: https://staging.federalportal.gov/register
- Browser: Chrome 124
- Screen Reader: JAWS 2024
- OS: Windows 10
- Build: v2.4.1

**Relates to:** Section 508 / WCAG 2.1 SC 4.1.3 – Status Messages

**Description:**  
When form validation errors appear after clicking **Register**, the inline error messages are visually rendered but are not announced by JAWS. Users relying on screen readers are unaware that errors have occurred and cannot identify which fields require correction.

**Steps to Reproduce:**
1. Open JAWS 2024
2. Navigate to https://staging.federalportal.gov/register
3. Leave required fields empty
4. Tab to the **Register** button and press Enter
5. Listen for JAWS announcements

**Actual Result:**  
JAWS does not announce any error messages. The screen reader focus remains on the Register button with no feedback about validation failures.

**Expected Result:**  
Error messages should be announced by JAWS automatically using an ARIA live region (`role="alert"` or `aria-live="assertive"`). Focus should move to the first field with an error.

**Impact:**  
Users with visual disabilities cannot complete registration, creating a 508 compliance violation and excluding a segment of the user population.

**Attachments:**  
- `screenshot_jaws_no_announcement.png`
- `html_source_missing_aria_live.txt`

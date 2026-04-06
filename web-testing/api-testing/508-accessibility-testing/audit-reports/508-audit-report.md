# Section 508 / WCAG 2.1 Accessibility Audit Report

**Application:** Federal Web Portal – Public-Facing Homepage & Contact Form  
**Auditor:** Yoftahe Wordofa | DHS Trusted Tester V5.1.3  
**Audit Date:** April 3, 2025  
**Standard:** Section 508 (WCAG 2.1 Level AA)  
**Testing Method:** Manual (ANDI, JAWS 2024, Keyboard-only) + Automated (WAVE, axe DevTools)  

---

## 1. Executive Summary

This accessibility audit was conducted on the public-facing homepage and contact form of a federal web portal. The audit evaluated conformance with Section 508 standards and WCAG 2.1 Level AA success criteria.

| Category | Finding |
|---|---|
| Total Issues Found | 14 |
| Critical (Must Fix) | 5 |
| High | 4 |
| Medium | 3 |
| Low / Advisory | 2 |
| Overall Conformance | **Non-Conformant** |

**Summary:** The application currently does not meet Section 508 / WCAG 2.1 AA requirements. Five critical issues — including missing image alternative text, keyboard trap in a modal dialog, and absent form labels — must be resolved before the application can be considered compliant.

---

## 2. Testing Environment

| Item | Details |
|---|---|
| URL Tested | https://staging.federalportal.gov |
| Pages Tested | Homepage, Contact Form, Navigation Menu, Modal Dialog |
| Browser | Chrome 124 |
| OS | Windows 10 |
| Screen Reader | JAWS 2024 |
| Automated Tools | WAVE (webaim.org/wave), axe DevTools v4.9 |
| Manual Tool | ANDI (Accessible Name & Description Inspector) |
| Keyboard Testing | Keyboard-only navigation (Tab, Shift+Tab, Enter, Space, Arrow keys) |

---

## 3. Detailed Findings

---

### FINDING-001 — Missing Alternative Text on Informational Images

**WCAG Success Criterion:** 1.1.1 Non-text Content  
**Section 508 Criteria:** 501 (Web) / 504 (Authoring Tools)  
**Severity:** Critical  
**Page:** Homepage  

**Description:**  
Seven informational images on the homepage (agency logos, program icons, and a banner graphic) have no `alt` attribute or have empty `alt=""` despite being informational and conveying meaning to the user.

**How Identified:** ANDI flagged missing alt text; JAWS reads the image file name instead of a description.

**JAWS Output (Actual):** *"banner_hero_2024_v2.jpg graphic"*  
**JAWS Output (Expected):** *"[Meaningful description of what the image depicts]"*

**Code Sample (Actual):**
```html
<img src="banner_hero_2024_v2.jpg">
```

**Recommended Fix:**
```html
<img src="banner_hero_2024_v2.jpg" alt="Federal portal representatives assisting citizens at a service center">
```

---

### FINDING-002 — Keyboard Trap in Modal Dialog

**WCAG Success Criterion:** 2.1.2 No Keyboard Trap  
**Section 508 Criteria:** 501 (Web)  
**Severity:** Critical  
**Page:** Homepage (Newsletter Signup Modal)  

**Description:**  
When the "Subscribe" modal dialog is opened via keyboard, focus enters the modal but cannot be moved out of the modal using the keyboard. The Escape key does not close the modal. The only way to exit is to reload the page.

**Steps to Reproduce:**
1. Tab to the **Subscribe** button and press Enter
2. Modal opens — focus moves into the modal
3. Attempt to press Escape to close the modal
4. Attempt to Tab past the modal content

**Actual Result:** Escape does not close the modal. Tab cycles only within a single input field. User cannot exit the modal via keyboard.

**Expected Result:** The Escape key should close the modal and return focus to the trigger element. Focus should be trapped within the modal while open (per ARIA modal pattern) but must be releasable via Escape.

**Recommended Fix:** Implement ARIA modal pattern with `role="dialog"`, `aria-modal="true"`, focus trap within modal, and Escape key handler that closes the modal and returns focus.

---

### FINDING-003 — Form Fields Missing Programmatic Labels

**WCAG Success Criterion:** 1.3.1 Info and Relationships / 4.1.2 Name, Role, Value  
**Section 508 Criteria:** 501 (Web)  
**Severity:** Critical  
**Page:** Contact Form  

**Description:**  
The Contact Form contains five input fields (Name, Email, Subject, Phone, Message) that use visible placeholder text as their only label. No `<label>` element, `aria-label`, or `aria-labelledby` is associated with any input. When a user starts typing, the placeholder disappears and there is no accessible label remaining.

**ANDI Finding:** "No accessible name found for input field."

**Code Sample (Actual):**
```html
<input type="text" placeholder="Enter your name">
```

**Recommended Fix:**
```html
<label for="name">Name</label>
<input type="text" id="name" placeholder="e.g. Jane Smith">
```

---

### FINDING-004 — Insufficient Color Contrast on Navigation Links

**WCAG Success Criterion:** 1.4.3 Contrast (Minimum)  
**Section 508 Criteria:** 501 (Web)  
**Severity:** High  
**Page:** All pages (Global Navigation)  

**Description:**  
Navigation link text (#767676 gray on #FFFFFF white background) has a contrast ratio of approximately **4.48:1**, which falls below the required **4.5:1** minimum for normal-sized text.

| Element | Foreground | Background | Actual Ratio | Required | Pass/Fail |
|---|---|---|---|---|---|
| Nav links | #767676 | #FFFFFF | 4.48:1 | 4.5:1 | ❌ Fail |
| Footer links | #6B7280 | #F9FAFB | 4.12:1 | 4.5:1 | ❌ Fail |
| Error messages | #DC2626 | #FFFFFF | 5.74:1 | 4.5:1 | ✅ Pass |

**Recommended Fix:** Update navigation link color to `#757575` or darker (minimum 4.5:1 ratio). Recommended: `#595959` (7:1 ratio — AAA compliant).

---

### FINDING-005 — Page Lacks Skip Navigation Link

**WCAG Success Criterion:** 2.4.1 Bypass Blocks  
**Section 508 Criteria:** 501 (Web)  
**Severity:** High  
**Page:** All pages  

**Description:**  
There is no "Skip to main content" or equivalent skip navigation mechanism. Keyboard and screen reader users must Tab through all navigation links (23 links) on every page load before reaching the main content area.

**Recommended Fix:** Add a visually hidden skip link as the first focusable element on every page:
```html
<a href="#main-content" class="skip-link">Skip to main content</a>
<main id="main-content">...</main>
```
```css
.skip-link {
  position: absolute;
  left: -9999px;
}
.skip-link:focus {
  left: 0;
  top: 0;
  z-index: 9999;
}
```

---

### FINDING-006 — Focus Indicator Not Visible on Interactive Elements

**WCAG Success Criterion:** 2.4.7 Focus Visible  
**Section 508 Criteria:** 501 (Web)  
**Severity:** High  
**Page:** Homepage, Contact Form  

**Description:**  
CSS `outline: none` has been applied to all focusable elements (buttons, links, inputs), removing the browser's default focus ring. Keyboard users cannot determine which element currently has focus.

**Recommended Fix:** Remove `outline: none` from global CSS and implement a custom, high-contrast focus indicator:
```css
:focus-visible {
  outline: 3px solid #0066CC;
  outline-offset: 2px;
}
```

---

### FINDING-007 — Images of Text Used for Headings

**WCAG Success Criterion:** 1.4.5 Images of Text  
**Section 508 Criteria:** 501 (Web)  
**Severity:** Medium  
**Page:** Homepage  

**Description:**  
Three section headings are rendered as images of text rather than actual HTML text. These images scale poorly, cannot be resized by users with low vision, and are not readable by screen readers without proper alt text.

**Recommended Fix:** Replace image-based headings with styled HTML heading elements (`<h2>`, `<h3>`) with CSS for visual styling.

---

## 4. Summary Table

| ID | WCAG SC | Description | Severity | Status |
|---|---|---|---|---|
| FINDING-001 | 1.1.1 | Missing alt text on images | Critical | Open |
| FINDING-002 | 2.1.2 | Keyboard trap in modal | Critical | Open |
| FINDING-003 | 1.3.1 / 4.1.2 | Form fields missing labels | Critical | Open |
| FINDING-004 | 1.4.3 | Insufficient color contrast | High | Open |
| FINDING-005 | 2.4.1 | No skip navigation link | High | Open |
| FINDING-006 | 2.4.7 | Focus indicator not visible | High | Open |
| FINDING-007 | 1.4.5 | Images of text for headings | Medium | Open |

---

## 5. Recommendations

1. **Immediate (Sprint 1):** Address all Critical findings (FINDING-001 through 003) before next release
2. **Short-term (Sprint 2):** Resolve all High findings (004–006)
3. **Ongoing:** Integrate axe DevTools into CI/CD pipeline to catch regressions automatically
4. **Training:** Conduct developer accessibility training focusing on ARIA, semantic HTML, and keyboard patterns

---

## 6. Auditor Sign-Off

**Auditor:** Yoftahe Wordofa  
**Certification:** DHS Trusted Tester V5.1.3  
**Date:** April 3, 2025  
**Signature:** ________________________

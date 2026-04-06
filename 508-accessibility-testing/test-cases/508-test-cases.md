# Section 508 / WCAG 2.1 Test Cases

**Module:** Full Application – Accessibility Conformance Testing  
**Prepared By:** Yoftahe Wordofa | DHS Trusted Tester V5.1.3  
**Standard:** Section 508 / WCAG 2.1 Level AA  
**Tools:** ANDI, JAWS 2024, WAVE, axe DevTools, Keyboard-only navigation  

---

## TC-508-001 — Keyboard Navigation: All Interactive Elements Reachable

**WCAG SC:** 2.1.1 Keyboard  
**Priority:** P1  
**Test Type:** Manual – Keyboard Only  

**Preconditions:**
- Mouse is disconnected or disabled
- Application is loaded in Chrome

**Steps:**
1. Place focus at the top of the page
2. Press Tab repeatedly to navigate through all interactive elements
3. Press Shift+Tab to navigate in reverse
4. Activate links and buttons using Enter
5. Activate checkboxes and radio buttons using Space
6. Navigate dropdowns using Arrow keys

**Expected Result:**
- Every interactive element (links, buttons, inputs, dropdowns, checkboxes, radio buttons) is reachable via Tab
- No element is skipped or unreachable
- Keyboard activation works correctly for all element types
- Tab order follows a logical reading order (left-to-right, top-to-bottom)

**Pass Criteria:** All interactive elements are keyboard accessible with logical tab order  
**Fail Criteria:** Any interactive element unreachable by keyboard, or illogical tab order

---

## TC-508-002 — Keyboard Navigation: No Keyboard Trap

**WCAG SC:** 2.1.2 No Keyboard Trap  
**Priority:** P1  
**Test Type:** Manual – Keyboard Only  

**Steps:**
1. Tab to any modal dialog, popup, widget, or embedded component
2. Attempt to move focus out using Tab, Shift+Tab, and Escape
3. Test all modals and overlays on the page

**Expected Result:**
- Focus can always be moved away from any component using standard keyboard commands
- Escape key closes modal dialogs and returns focus to the trigger element
- No component permanently traps keyboard focus

**Pass Criteria:** User can freely move focus in and out of all components  
**Fail Criteria:** Any component that traps focus with no keyboard escape

---

## TC-508-003 — Focus Indicator Visible on All Interactive Elements

**WCAG SC:** 2.4.7 Focus Visible  
**Priority:** P1  
**Test Type:** Manual – Visual + Keyboard  

**Steps:**
1. Tab through all interactive elements on the page
2. Observe whether a visible focus indicator is present on each element when it receives focus

**Expected Result:**
- A visible focus indicator (outline, border, highlight) is present on every focused element
- The focus indicator has sufficient contrast (at least 3:1) against the adjacent background
- Focus indicator is never removed via CSS `outline: none` without a custom replacement

**Pass Criteria:** Focus indicator is visible on all interactive elements  
**Fail Criteria:** Any element where focus is not visually indicated

---

## TC-508-004 — Images Have Meaningful Alternative Text

**WCAG SC:** 1.1.1 Non-text Content  
**Priority:** P1  
**Test Type:** Manual – ANDI + JAWS  

**Steps:**
1. Open ANDI and activate the **Graphics/Images** module
2. Tab through all images on the page
3. Review ANDI's reported accessible name for each image
4. For decorative images, confirm `alt=""` and `role="presentation"` are set
5. Activate JAWS and navigate to each image to confirm announcement

**Expected Result:**
- Informational images: ANDI and JAWS announce a meaningful, concise description
- Decorative images: JAWS skips entirely (alt="")
- Images of text: alt text matches the text in the image
- Complex images (charts, graphs): have a short alt text and a longer description nearby or via `aria-describedby`

**Pass Criteria:** All images have appropriate, meaningful alternative text  
**Fail Criteria:** Any informational image with missing, empty, or meaningless alt text (e.g., filename)

---

## TC-508-005 — Form Fields Have Programmatic Labels

**WCAG SC:** 1.3.1 Info and Relationships / 4.1.2 Name, Role, Value  
**Priority:** P1  
**Test Type:** Manual – ANDI + JAWS  

**Steps:**
1. Open ANDI and activate the **Focusable Elements** module
2. Tab to each form input field
3. Review ANDI's reported accessible name for each field
4. Activate JAWS and Tab to each input field
5. Listen for JAWS announcement of field label

**Expected Result:**
- Every input field has a programmatic label via `<label for>`, `aria-label`, or `aria-labelledby`
- JAWS announces the field label when focus enters the field (e.g., *"Email address, edit"*)
- Labels remain visible and associated even when placeholder text disappears on input

**Pass Criteria:** All form fields have programmatic labels announced by JAWS  
**Fail Criteria:** Any field where JAWS announces only "edit" or "blank" without a label

---

## TC-508-006 — Color Contrast Meets Minimum Requirements

**WCAG SC:** 1.4.3 Contrast (Minimum)  
**Priority:** P1  
**Test Type:** Manual – Colour Contrast Analyzer / axe DevTools  

**Steps:**
1. Run axe DevTools scan on each page and review color contrast failures
2. Use Colour Contrast Analyzer to manually test any flagged or suspicious text elements
3. Record foreground color, background color, contrast ratio, and text size for each element

**Expected Result:**
- Normal text (< 18pt or < 14pt bold): minimum contrast ratio of **4.5:1**
- Large text (≥ 18pt or ≥ 14pt bold): minimum contrast ratio of **3:1**
- UI components and focus indicators: minimum contrast ratio of **3:1**
- Text in images meets the same contrast requirements

**Pass Criteria:** All text and UI components meet minimum contrast ratios  
**Fail Criteria:** Any text or UI component below the required contrast threshold

---

## TC-508-007 — Page Has Logical Heading Structure

**WCAG SC:** 1.3.1 Info and Relationships  
**Priority:** P2  
**Test Type:** Manual – ANDI + WAVE  

**Steps:**
1. Open WAVE and review the heading structure report
2. Open ANDI, activate the **Structure** module, and review headings
3. Verify one `<h1>` exists per page (the main page title)
4. Verify headings do not skip levels (e.g., h1 → h3 skipping h2)

**Expected Result:**
- One `<h1>` per page representing the main page title
- Headings follow a logical hierarchy (h1 → h2 → h3)
- No heading levels are skipped
- Headings are used for structure, not for visual styling

**Pass Criteria:** Logical, properly nested heading hierarchy with one h1  
**Fail Criteria:** Missing h1, skipped heading levels, or headings used purely for styling

---

## TC-508-008 — Screen Reader Announces Dynamic Content Updates

**WCAG SC:** 4.1.3 Status Messages  
**Priority:** P1  
**Test Type:** Manual – JAWS  

**Steps:**
1. Activate JAWS 2024
2. Submit a form and listen for success/error messages
3. Trigger any dynamic content change (loading spinner, filtered results, alerts)
4. Listen for JAWS announcements without moving focus

**Expected Result:**
- Success and error messages are announced by JAWS automatically without requiring the user to move focus
- Dynamic content changes are communicated via ARIA live regions (`aria-live="polite"` or `aria-live="assertive"`)
- Loading spinners or progress indicators are announced (e.g., *"Loading, please wait"*)

**Pass Criteria:** All status messages and dynamic updates are announced by JAWS  
**Fail Criteria:** Any status message or dynamic content update not announced by JAWS

---

## TC-508-009 — Videos Have Synchronized Captions

**WCAG SC:** 1.2.2 Captions (Prerecorded)  
**Priority:** P1  
**Test Type:** Manual – Visual  

**Steps:**
1. Navigate to all pages containing video content
2. Play each video
3. Check whether closed captions are available
4. Enable captions and verify synchronization and accuracy

**Expected Result:**
- All prerecorded videos have a closed caption option
- Captions are synchronized with the audio (within 2 seconds)
- Captions include all spoken dialogue and relevant non-speech audio (e.g., [applause], [music])
- Caption accuracy is ≥ 99%

**Pass Criteria:** All videos have accurate, synchronized closed captions  
**Fail Criteria:** Any video with missing, inaccurate, or unsynchronized captions

---

## TC-508-010 — PDF Documents Are Tagged and Accessible

**WCAG SC:** 1.3.1 Info and Relationships  
**Section 508:** 501 (Web)  
**Priority:** P1  
**Test Type:** Manual – JAWS + Adobe Acrobat  

**Steps:**
1. Open each linked PDF document
2. In Adobe Acrobat, go to **Tools > Accessibility > Full Check**
3. Review the accessibility report
4. Open the PDF with JAWS and navigate using arrow keys and heading shortcuts

**Expected Result:**
- PDF is tagged with proper structure (headings, paragraphs, lists, tables)
- Document language is set
- Reading order is logical
- All images have alt text
- Tables have header row associations
- JAWS reads all content in logical reading order

**Pass Criteria:** PDF passes Acrobat Accessibility Check with no critical failures; JAWS reads all content  
**Fail Criteria:** Untagged PDF, JAWS unable to read content, or critical Acrobat accessibility failures

# Section 508 Bug Reports

**Project:** Federal Web Portal – Section 508 / WCAG 2.1 AA Compliance  
**Prepared By:** Yoftahe Wordofa | DHS Trusted Tester V5.1.3  
**Tool:** Jira  

---

## 508-BUG-001 — Screen Reader Does Not Announce Dropdown Menu Items

**Severity:** Critical  
**Priority:** P1  
**Status:** Open  
**Reported By:** Yoftahe Wordofa  
**Date:** April 3, 2025  

**WCAG Success Criterion:** 4.1.2 – Name, Role, Value  
**Section 508 Requirement:** 501 (Web)  

**Environment:**
- URL: https://staging.federalportal.gov
- Browser: Chrome 124
- Screen Reader: JAWS 2024
- OS: Windows 10
- Build: v2.4.1

**Description:**  
The primary navigation dropdown menus are implemented using `<div>` and `<span>` elements with JavaScript click handlers instead of native HTML elements or appropriate ARIA roles. When a dropdown is opened via keyboard, JAWS does not announce the expanded state, the number of items, or the individual menu item names.

**Steps to Reproduce:**
1. Launch JAWS 2024
2. Open Chrome and navigate to https://staging.federalportal.gov
3. Press Tab to move focus to the first navigation item **"Programs"**
4. Press Enter or Space to activate the dropdown
5. Listen to JAWS output

**Actual Result:**  
JAWS announces *"Programs"* when focused but provides no announcement when the dropdown expands. Individual dropdown items are not announced when tabbed through. The expanded/collapsed state is not communicated.

**Expected Result:**  
- JAWS should announce *"Programs, collapsed, button"* when focused
- After activation: *"Programs, expanded, button"* and announce the submenu
- Each item should be announced with its name and role (e.g., *"Benefits Overview, link, 1 of 5"*)

**Root Cause (Developer Note):**  
Navigation uses `<div tabindex="0">` without `role="button"` or `aria-expanded`. Submenu items use `<span>` without `role="menuitem"`.

**Recommended Fix:**
```html
<button aria-haspopup="true" aria-expanded="false" id="programs-btn">
  Programs
</button>
<ul role="menu" aria-labelledby="programs-btn">
  <li role="none"><a role="menuitem" href="/programs/benefits">Benefits Overview</a></li>
</ul>
```

**Attachments:**
- `screenshot_dropdown_no_aria.png`
- `jaws_log_no_announcement.txt`
- `html_source_nav_markup.txt`

---

## 508-BUG-002 — Data Table Has No Header Associations

**Severity:** High  
**Priority:** P1  
**Status:** Open  
**Reported By:** Yoftahe Wordofa  
**Date:** April 3, 2025  

**WCAG Success Criterion:** 1.3.1 – Info and Relationships  
**Section 508 Requirement:** 501 (Web)  

**Environment:**
- URL: https://staging.federalportal.gov/reports
- Browser: Chrome 124
- Screen Reader: JAWS 2024
- OS: Windows 10

**Description:**  
The Program Reports data table on the Reports page does not use `<th>` elements for column headers and has no `scope` or `headers` attributes. When navigating table cells with JAWS, the screen reader reads only the cell data without providing header context, making the table data meaningless for screen reader users.

**Steps to Reproduce:**
1. Launch JAWS 2024
2. Navigate to https://staging.federalportal.gov/reports
3. Tab to the data table
4. Use JAWS table navigation (Ctrl+Alt+Arrow keys) to move between cells

**Actual Result:**  
JAWS announces only the cell value (e.g., *"4,521"*) with no column or row header context. Users cannot determine what the value represents without visually scanning the table.

**Expected Result:**  
JAWS should announce the cell value along with its header context (e.g., *"Total Applicants, Q1 2025, 4,521"*).

**Code Sample (Actual):**
```html
<table>
  <tr>
    <td>Program</td>
    <td>Q1 2025</td>
    <td>Q2 2025</td>
  </tr>
  <tr>
    <td>Benefits</td>
    <td>4,521</td>
    <td>5,102</td>
  </tr>
</table>
```

**Recommended Fix:**
```html
<table>
  <caption>Program Applications by Quarter</caption>
  <thead>
    <tr>
      <th scope="col">Program</th>
      <th scope="col">Q1 2025</th>
      <th scope="col">Q2 2025</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th scope="row">Benefits</th>
      <td>4,521</td>
      <td>5,102</td>
    </tr>
  </tbody>
</table>
```

**Attachments:**
- `screenshot_table_no_headers.png`
- `jaws_output_table_cells.txt`

---

## 508-BUG-003 — PDF Document Is Not Tagged (Not Screen Reader Accessible)

**Severity:** High  
**Priority:** P1  
**Status:** Open  
**Reported By:** Yoftahe Wordofa  
**Date:** April 3, 2025  

**WCAG Success Criterion:** 1.3.1 – Info and Relationships  
**Section 508 Requirement:** 504 (Authoring Tools) / 501 (Web)  

**Environment:**
- URL: https://staging.federalportal.gov/resources/annual-report.pdf
- Screen Reader: JAWS 2024
- OS: Windows 10

**Description:**  
The Annual Report PDF linked on the Resources page is an untagged PDF. It was likely created by scanning a printed document as an image. JAWS cannot read any content from the document, and the PDF is entirely inaccessible to screen reader users.

**Steps to Reproduce:**
1. Launch JAWS 2024
2. Navigate to the Resources page
3. Click the **Annual Report (PDF)** link
4. PDF opens in browser
5. Press Ctrl+A to select all text, then attempt to navigate with JAWS

**Actual Result:**  
JAWS announces *"blank"* or reads nothing at all. No text content is accessible. Document appears as a single scanned image.

**Expected Result:**  
The PDF should be fully tagged with proper reading order, heading structure, alternative text for images, and table markup. All text content should be readable by JAWS.

**Recommended Fix:**
- Run OCR on the scanned PDF using Adobe Acrobat Pro
- Add document tags using Acrobat's Accessibility Checker and Make Accessible wizard
- Set document language, title, and reading order
- Validate with PAC 2024 (PDF Accessibility Checker)

**Attachments:**
- `screenshot_pdf_jaws_blank.png`
- `acrobat_accessibility_report.pdf`

---

## 508-BUG-004 — Video Has No Captions

**Severity:** Critical  
**Priority:** P1  
**Status:** Open  
**Reported By:** Yoftahe Wordofa  
**Date:** April 3, 2025  

**WCAG Success Criterion:** 1.2.2 – Captions (Prerecorded)  
**Section 508 Requirement:** 501 (Web) / 503 (Applications)  

**Environment:**
- URL: https://staging.federalportal.gov/about/video-overview
- Browser: Chrome 124
- OS: Windows 10

**Description:**  
A 4-minute program overview video on the About page has no closed captions or subtitles. Audio-only content (spoken narration) is entirely inaccessible to users who are deaf or hard of hearing.

**Steps to Reproduce:**
1. Navigate to https://staging.federalportal.gov/about/video-overview
2. Play the video
3. Observe whether any caption or subtitle track is available

**Actual Result:**  
No captions are available. The video player shows no caption toggle button. Audio content is completely inaccessible to users who rely on captions.

**Expected Result:**  
Accurate, synchronized captions must be available for all prerecorded audio content. A caption toggle button should be present in the video player controls.

**Recommended Fix:**
- Generate a synchronized caption file (.vtt or .srt format)
- Implement using the HTML `<track>` element:
```html
<video controls>
  <source src="program-overview.mp4" type="video/mp4">
  <track kind="captions" src="captions-en.vtt" srclang="en" label="English" default>
</video>
```
- Verify caption accuracy (min. 99% word accuracy for federal content)

**Attachments:**
- `screenshot_video_no_captions.png`

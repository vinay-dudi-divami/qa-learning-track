# Non-Functional Testing Plan
## Vendor Invoice Management Portal

| | |
|---|---|
| **Document ID** | QA-NFT-VIMP-001 |
| **Version** | 1.0 |
| **Prepared By** | Senior QA Engineer |
| **Date** | 15 May 2026 |

---

## Overview

Non-functional testing validates that the system meets quality attributes beyond functional correctness. A product can pass every functional test and still fail in production due to poor performance, a security vulnerability, or an inaccessible UI.

> *"Does it work?" is only half the question. "Does it hold up?" is the other half.*

This plan covers four domains: **Performance**, **Security**, **Accessibility**, and **Usability**.

---

## 1. Performance Testing

**Tool:** Apache JMeter / k6  
**Environment:** Staging (production-equivalent infrastructure)  
**Baseline:** Established in the first load test run; subsequent runs compared against it

### Test Scenarios

| Test ID | Type | Scenario | Success Criteria |
|---|---|---|---|
| PERF-001 | Load Test | 500 concurrent vendors submitting invoices simultaneously | 95th percentile response ≤3s; zero errors; CPU <80% |
| PERF-002 | Stress Test | Ramp to 1000 users; find system breaking point; observe recovery | System degrades gracefully; recovers within 60s after load removed |
| PERF-003 | Spike Test | Sudden spike from 100 to 800 users in 30 seconds | No crash; error rate <2% at spike peak |
| PERF-004 | Soak Test | 200 concurrent users for 4 hours continuously | No memory leaks; response time stable throughout; DB connection pool stable |
| PERF-005 | Login Response | 100 concurrent logins | Login API response ≤1 second at 100 concurrent users |
| PERF-006 | File Upload Throughput | 50 concurrent 5 MB invoice uploads | Upload completes within 10s; no file corruption; storage 100% consistent |

### Key Metrics to Capture

- Response time (average, 95th percentile, 99th percentile)
- Error rate (%)
- Throughput (requests/second)
- CPU and memory utilization on app and DB servers
- Database connection pool usage
- Garbage collection frequency (for JVM-based backends)

### Performance Test Execution Notes

- Run tests during off-peak hours to avoid interference
- Clear application cache before each test run
- Monitor server resources in real time (Datadog / CloudWatch)
- Capture results at: ramp-up, steady state, and ramp-down phases

---

## 2. Security Testing

**Tools:** OWASP ZAP (automated scan), Burp Suite Community (manual), Manual testing  
**Standard:** OWASP Top 10 (2021)

### OWASP Top 10 Test Coverage

| Test ID | Vulnerability | Severity | Approach | Pass Criteria |
|---|---|---|---|---|
| SEC-001 | SQL Injection | Critical | Inject payloads in all input fields (login, invoice number, PO, search fields) | All attempts return 400/validation error; no SQL executed in DB logs |
| SEC-002 | XSS (Stored & Reflected) | High | Input `<script>alert('XSS')</script>` and HTML entities in all text fields; verify rendering | Script not executed; input sanitized and rendered as plain text |
| SEC-003 | Broken Authentication | Critical | Test brute force without lockout; session token predictability; JWT `alg:none` attack | Account lockout enforced; JWT signature validated; no token reuse |
| SEC-004 | Insecure Direct Object Reference (IDOR) | Critical | Change invoice_id in URL from `/invoices/1001` to `/invoices/1002` (another vendor's record) | HTTP 403 returned; no cross-vendor data exposed |
| SEC-005 | Security Misconfiguration | High | Check for debug endpoints, default credentials, verbose stack traces in error responses, open CORS | No debug info exposed; CORS restricted to known origins only |
| SEC-006 | Sensitive Data Exposure | High | Verify HTTPS enforced; passwords not logged; bank account details masked in UI and API responses | All traffic HTTPS; sensitive fields masked or redacted in logs and responses |
| SEC-007 | Broken Access Control | Critical | Test all RBAC boundaries: vendor accessing AP URLs, AP user accessing admin functions, unauthenticated API calls | HTTP 403 on unauthorized; HTTP 401 on unauthenticated; all role boundaries enforced |

### Additional Security Checks

- **Session fixation:** Verify session token regenerates after login
- **Clickjacking:** Verify `X-Frame-Options: DENY` or `CSP: frame-ancestors 'none'` header present
- **File upload security:** Upload PHP/executable disguised as PDF; verify server rejects or does not execute
- **Rate limiting:** Verify API rate limiting on login, invoice submission, and report generation endpoints
- **Password storage:** Verify passwords are hashed with bcrypt/argon2 (not MD5/SHA1); confirm via code review

### Security Testing Sign-off Requirement

All Critical and High severity security findings must be remediated before go-live. A final OWASP ZAP scan report must be attached to the go-live approval document.

---

## 3. Accessibility Testing (WCAG 2.1 AA)

**Tools:** axe DevTools (browser extension), NVDA screen reader + Chrome, Manual keyboard testing  
**Standard:** WCAG 2.1 Level AA

### Test Coverage

| Test ID | Criterion | Test Approach | Pass Criteria |
|---|---|---|---|
| A11Y-001 | Keyboard Navigation | Complete all critical workflows (login, invoice submission, AP approval) using keyboard only — Tab, Shift+Tab, Enter, Escape | No mouse required for any critical workflow; no keyboard traps |
| A11Y-002 | Screen Reader | Test with NVDA + Chrome: verify all form labels, buttons, error messages, and status changes are announced | All interactive elements have meaningful accessible names; errors announced clearly |
| A11Y-003 | Colour Contrast | Use axe DevTools to verify text contrast ratios across all pages | Normal text ≥4.5:1; large text ≥3:1; all pass WCAG AA |
| A11Y-004 | Error Identification | Trigger validation errors; verify they are programmatically associated with their input field via `aria-describedby` or `aria-invalid` | Screen reader announces both the field label and the specific error message |
| A11Y-005 | Focus Management | Open modal dialogs (approval, rejection); verify focus moves to modal on open and returns to trigger on close | Focus not lost; no focus trap outside intentional modals |
| A11Y-006 | Resizable Text | Zoom browser to 200%; verify no horizontal scrolling, content overflow, or loss of functionality | All content accessible and usable at 200% zoom |

### Accessibility Testing Notes

- Run axe DevTools on every major page before marking functional testing complete
- Any Critical or Serious axe finding counts as a Medium defect minimum
- Screen reader testing should be conducted on the final UI build, not wireframes

---

## 4. Usability Testing

**Method:** Exploratory manual testing; task-based evaluation  
**Participants:** QA Engineer acting as first-time user persona; ideally supplemented with 1–2 real end users

### Usability Scenarios

| Test ID | Persona | Task | Success Criteria |
|---|---|---|---|
| USA-001 | First-Time Vendor | Complete invoice submission without reading any documentation | Task completed in ≤5 minutes with zero errors and no external guidance |
| USA-002 | Frustrated User | Trigger form validation errors; assess recovery path | All error messages are specific, actionable, and human-readable; user can correct without refreshing |
| USA-003 | Mobile Vendor | Submit an invoice on a mobile device (375px viewport, portrait) | No horizontal scrolling; all buttons tappable; form fully usable; file upload works |
| USA-004 | AP Manager | Locate, review, and approve an invoice in under 3 minutes | Approval workflow is intuitive; no ambiguous labels or hidden actions |
| USA-005 | Business User | Generate and download a monthly invoice activity report | Report accessible within 2 clicks; data matches expectations; download works |

### Usability Red Flags to Watch For

- Error messages that say "Error occurred" with no actionable detail
- Form labels that are vague or missing (e.g., a field labelled "Number" — which number?)
- Actions with no confirmation step (e.g., reject invoice with a single click, no dialog)
- Status labels that are ambiguous (e.g., "Pending" — pending what? By whom?)
- Success states that are silent (form submits but no confirmation toast or redirect)

---

## 5. Compatibility Testing

**Tool:** BrowserStack

| Platform | Browsers / OS | Priority |
|---|---|---|
| Desktop — Windows | Chrome (latest), Firefox (latest), Edge (latest) | High |
| Desktop — macOS | Safari (latest), Chrome (latest) | High |
| Mobile — iOS | Safari on iPhone 14 (iOS 17) | High |
| Mobile — Android | Chrome on Pixel 7 (Android 14) | High |
| Tablet | Safari on iPad (iOS 17); Chrome on Android tablet | Medium |
| Older browsers | Chrome n-1, Firefox n-1 | Medium |

**Focus areas for compatibility testing:**
- Invoice PDF upload (file picker behavior varies by OS/browser)
- Date picker UI (renders differently across browsers)
- Table layout in RTM / report views
- Modal dialog behavior on mobile
- Form autofill interactions

---

## 6. NFT Sign-off Criteria

| Domain | Minimum Pass Bar for Go-Live |
|---|---|
| Performance | PERF-001 (load test) passes; no Critical performance defects open |
| Security | Zero Critical (P1) security vulnerabilities; all OWASP Top 10 checks completed |
| Accessibility | Zero Critical axe findings on core user flows; WCAG AA confirmed for login, submission, and approval |
| Usability | USA-001 and USA-002 pass; no usability blocker defects open |
| Compatibility | Core workflows pass on Chrome, Firefox, Safari, and iOS Chrome |

---

*"Does it work?" is only half the question. "Does it hold up under real conditions?" is what separates good software from great software.*

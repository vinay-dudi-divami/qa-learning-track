# Test Strategy
## Vendor Invoice Management Portal

| | |
|---|---|
| **Document ID** | QA-STRAT-VIMP-001 |
| **Version** | 1.0 |
| **Prepared By** | Senior QA Engineer |
| **Date** | 15 May 2026 |
| **Project** | Vendor Invoice Management Portal |
| **Status** | Final |

---

## 1. Purpose

This document defines the overarching QA approach, standards, tools, and principles governing all testing activities for the Vendor Invoice Management Portal. It applies across all 8 phases of the QA lifecycle — from Requirements Analysis through Deployment Verification.

> **Core principle:** Quality is a team responsibility, not a QA department function. Testing is integrated from day one, not bolted on at the end.

---

## 2. QA Approach

QA follows a **Shift-Left** philosophy — defects are caught at the earliest possible phase, where the cost to fix is lowest.

Testing is conducted at multiple layers per feature:
- **Requirements layer** — review for ambiguity, gaps, and testability
- **API layer** — contract, auth, error codes, edge cases
- **UI layer** — user workflows, validation, error states
- **Database layer** — data persistence, constraints, integrity
- **Integration layer** — third-party service boundaries

---

## 3. Scope

### In Scope

- Vendor registration, login, session management
- Invoice submission, validation, file upload
- AP team approval/rejection workflow
- Payment forwarding integration (API layer)
- Email notification triggers and delivery
- Monthly report generation and access
- Role-based access control (RBAC)
- Performance: load up to 500 concurrent users
- Security: OWASP Top 10 vulnerability checks
- Cross-browser compatibility (Chrome, Firefox, Edge, Safari)
- Mobile responsiveness (portrait and landscape)
- WCAG 2.1 AA accessibility compliance
- Deployment smoke testing and configuration verification

### Out of Scope

- ERP / SAP internal configuration testing
- Bank gateway / payment rail testing (third-party responsibility)
- Native mobile application (unless confirmed in scope)
- Multi-language / localization (unless specified)
- Hardware infrastructure testing
- Automated regression suite (Phase 2 only)
- External penetration testing engagement

---

## 4. Testing Types

| Testing Type | Method / Tool | Approach |
|---|---|---|
| Functional Testing | Manual | Test all business workflows against acceptance criteria across UI, API, and DB layers |
| Regression Testing | Manual → Automated (Phase 2) | Re-run core workflows after every build to ensure existing functionality is unbroken |
| API Testing | Postman + Newman | Test all REST endpoints for contract correctness, auth validation, and payload edge cases |
| Database Testing | Manual — SQL queries | Verify data persistence, constraint enforcement, and no orphaned records |
| Performance Testing | JMeter / k6 | Load test with simulated concurrent users; validate response times and resource usage |
| Security Testing | OWASP ZAP + Burp Suite | OWASP Top 10 checks: injection, auth bypass, IDOR, data exposure, misconfiguration |
| Usability Testing | Manual — Exploratory | Evaluate user flows; identify friction, confusing labels, poor error messages |
| Accessibility Testing | axe DevTools + NVDA | Automated a11y scan + manual keyboard and screen reader validation |
| Compatibility Testing | BrowserStack | Chrome, Firefox, Edge, Safari (latest 2 versions); iOS Safari; Android Chrome |
| Smoke Testing | Manual post-deploy | 9 critical path test cases executed after every deployment to staging and production |
| UAT | Business stakeholders | AP Manager and Vendor representative execute defined UAT scenarios before go-live |

---

## 5. Tools

| Category | Tool | Purpose |
|---|---|---|
| Bug Tracking | Jira | Defect logging, tracking, sprint management |
| Test Management | TestRail / Xray for Jira | Test case management, execution tracking, coverage reporting |
| API Testing | Postman + Newman | Manual and automated API test execution |
| Performance | Apache JMeter / k6 | Load, stress, spike, and soak testing |
| Security | OWASP ZAP + Burp Suite Community | Automated vulnerability scanning + manual testing |
| Accessibility | axe DevTools + NVDA | Automated a11y scan + screen reader validation |
| Browser Testing | BrowserStack | Cross-browser and cross-device execution |
| CI Integration | GitHub Actions / Jenkins | Automated test run on PR and build pipeline (Phase 2) |
| DB Access | DBeaver + SQL client | Direct database validation queries |
| Reporting | Allure / TestRail Reports | Test execution dashboards and coverage metrics |

---

## 6. Defect Severity & Priority Matrix

| Severity | Priority | Definition | Example |
|---|---|---|---|
| **Critical** | P1 — Fix Now | System crash, data loss, security breach, complete feature failure. Release blocker. | Login returns 500 for all users |
| **High** | P2 — Fix in Sprint | Major feature broken; workaround exists; or data integrity at risk | Invoice amount validation not enforced |
| **Medium** | P3 — Fix in Next Sprint | Feature partially broken; incorrect error message; minor flow issue | Rejection email missing vendor name |
| **Low** | P4 — Fix When Possible | Minor cosmetic issue, spelling mistake, edge case with no data impact | Tooltip text has a typo |

---

## 7. QA Lifecycle Phases

QA adds value across all 8 phases:

| Phase | QA Activity |
|---|---|
| 1 — Requirements Analysis | Review requirements for ambiguity, missing edge cases, and testability |
| 2 — Test Planning | Define scope, schedule, risks, entry/exit criteria |
| 3 — Test Design | Write test scenarios, test cases, prepare test data |
| 4 — Shift-Left Dev Phase | Attend standups, review API contracts, clarify user stories |
| 5 — Functional Testing | Execute test cases across UI, API, and DB layers |
| 6 — Non-Functional Testing | Performance, security, accessibility, usability testing |
| 7 — Integration Testing | Test third-party service boundaries and failure scenarios |
| 8 — Deployment & Docs Verify | Smoke tests, configuration verification, documentation accuracy |

---

*QA begins at requirements and ends after deployment. Every phase is a QA responsibility.*

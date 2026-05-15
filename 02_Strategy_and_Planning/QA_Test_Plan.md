# Test Plan
## Vendor Invoice Management Portal

| | |
|---|---|
| **Document ID** | QA-PLAN-VIMP-001 |
| **Version** | 1.0 |
| **Prepared By** | Senior QA Engineer |
| **Date** | 15 May 2026 |
| **Project** | Vendor Invoice Management Portal |
| **Phase** | Phase 2 — Test Planning |

---

## 1. Entry & Exit Criteria

### Entry Criteria — Testing may begin when:

- [ ] All requirements reviewed and ambiguities resolved (RCD-VIMP-001 responses received)
- [ ] Test environment provisioned and accessible
- [ ] Test data prepared and seeded in test database
- [ ] Application build deployed to test environment
- [ ] Smoke test passes (critical path functional)
- [ ] All test cases reviewed and approved by QA Lead
- [ ] Required tools installed and access granted to QA team

### Exit Criteria — Testing is complete when:

- [ ] 100% of test cases executed
- [ ] Zero open Critical (P1) defects
- [ ] Zero open High (P2) defects affecting core workflows
- [ ] ≥95% test cases passed
- [ ] All Medium defects documented with an agreed fix plan
- [ ] UAT sign-off received from business stakeholders
- [ ] Test execution report and RTM completed and approved

---

## 2. Test Schedule & Milestones

| # | Phase Activity | Description | Duration | Owner |
|---|---|---|---|---|
| 1 | Requirements Analysis | Review all 7 requirements; raise 47 clarification questions; define traceability | 3 days | QA Lead |
| 2 | Test Planning | Document test strategy, plan, risks, entry/exit criteria | 2 days | QA Lead |
| 3 | Test Design | Write test scenarios and 65+ test cases; prepare test data | 5 days | QA Engineer |
| 4 | Shift-Left Review | Attend dev standups; review API contracts; clarify user stories | Ongoing | QA Engineer |
| 5 | Functional Testing | Execute all functional test cases across UI, API, and DB layers | 8 days | QA Engineer |
| 6 | Non-Functional Testing | Performance, security, accessibility, usability testing | 5 days | QA + Specialist |
| 7 | Integration Testing | Payment integration, email service, auth service boundary testing | 3 days | QA + Dev |
| 8 | Regression Testing | Re-run core regression suite after bug fixes | 3 days | QA Engineer |
| 9 | UAT Support | Support business users in UAT; log UAT defects | 4 days | QA + BA |
| 10 | Deployment Verification | Smoke test staging and production; verify config and docs | 1 day | QA Lead |

**Total estimated QA effort: ~34 person-days**

---

## 3. Risk Register

| Risk ID | Risk | Level | Mitigation Strategy |
|---|---|---|---|
| RSK-01 | Requirements not finalized before test design | HIGH | Begin test design on confirmed requirements only; use placeholder cases for open items |
| RSK-02 | Test environment instability | HIGH | Maintain a dedicated stable test environment; avoid shared dev environment |
| RSK-03 | Payment integration partner unavailable for testing | HIGH | Use mock/stub API responses for integration testing; validate real API on staging |
| RSK-04 | Insufficient test data (edge case data) | MEDIUM | Prepare synthetic test data scripts; include boundary values and invalid data sets |
| RSK-05 | Timeline compression forcing coverage cuts | HIGH | Apply risk-based testing; prioritize P1/P2 scenarios; document descoped tests |
| RSK-06 | Defect fix introducing new regressions | MEDIUM | Maintain regression suite; execute smoke + targeted regression after every fix |
| RSK-07 | UAT delayed due to business stakeholder availability | MEDIUM | Schedule UAT sessions early; provide self-service UAT guide with test data |

---

## 4. Test Environments

| Environment | Purpose | Owner | URL |
|---|---|---|---|
| Development | Dev-level unit testing; not used by QA | Dev Team | dev.vimp.internal |
| Staging / QA | Primary QA test environment; mirrors production config | QA Lead | staging.vimp.internal |
| UAT | Business user acceptance testing | BA + QA | uat.vimp.internal |
| Production | Post-deploy smoke testing only | DevOps | vimp.company.com |

> **Rule:** QA does not test on Development. Staging is the minimum environment for functional testing.

---

## 5. Test Data Strategy

| Data Category | Approach |
|---|---|
| Vendor accounts | Pre-seeded: active, locked, pending-approval states |
| Purchase Orders | Pre-seeded: open, closed, expired, partially-billed POs |
| Invoice files | Prepared PDFs at boundary sizes (4.9 MB, 5.0 MB, 5.1 MB); invalid formats |
| AP users | Pre-seeded with different role levels (Reviewer, Manager) |
| Edge case data | Script-generated: future dates, zero amounts, special characters, SQL injection strings |
| Production data | **Never used in testing.** Synthetic data only. |

---

## 6. Roles & Responsibilities

| Role | Person | Responsibilities |
|---|---|---|
| QA Lead | — | Strategy, planning, test case review, risk management, stakeholder reporting |
| QA Engineer | — | Test case writing, execution, bug reporting, regression testing |
| Developer | — | Unit testing, API contract review, bug fixes, shift-left collaboration |
| Business Analyst | — | Requirements clarification, UAT coordination, acceptance criteria sign-off |
| AP Manager | — | UAT execution, business sign-off |
| DevOps | — | Environment provisioning, deployment, monitoring setup |

---

*A plan without risk analysis is just a wish list.*

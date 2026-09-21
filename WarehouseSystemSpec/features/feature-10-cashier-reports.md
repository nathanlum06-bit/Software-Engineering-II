# Feature: Cashier Reports
**Feature ID:** 10
**Branch pattern:** `feature/10-cashier-reports`
**Status:** Ready
**Created:** 2026-07-25
**Input:** Generate cashier reports — sales and over/short for day and month (SRD
FR-10, Use Case Generate Report — Cashiers).
**Depends on:** [Feature 1 — User Authorization](feature-1-user-authorization.md),
[Feature 7 — Sales Session](feature-7-sales-session.md), [Feature 8 — Make a Sale]
(feature-8-make-sale.md)
**Related:** SRD Functional Requirement 10, [ADR-0001](../docs/adr/0001-object-
oriented-java.md), [ADR-0004](../docs/adr/0004-java-swing-ui.md),
[ADR-0005](../docs/adr/0005-junit.md), [cashier-report.png](../docs/ui/feature-10-
cashier-reports/cashier-report.png)
---
## User Stories
### US-10.1: View sales by cashier for day and month
**As a** manager
**I want to** see total sales by cashier for a day or month
**So that** I can judge cashier effectiveness
**Priority:** P1
**Independent test:** Call `CashierReportService.salesByCashierForDay(date)` or
`salesByCashierForMonth(yearMonth)`; aggregate completed sale totals by cashier for
period
**Acceptance scenarios:** see ### US-10.1 under Acceptance Criteria
### US-10.2: View over/short by cashier for day and month
**As a** manager
**I want to** see over/short by cashier for a day or month
**So that** I can assess cash accuracy and potential theft
**Priority:** P1
**Independent test:** Call `CashierReportService.overShortByCashierForDay(date)` or
`overShortByCashierForMonth(yearMonth)`; sum session over/short amounts by cashier
for period
**Acceptance scenarios:** see ### US-10.2 under Acceptance Criteria
---
## Requirements
### Functional Requirements
- **FR-001**: System MUST report sales for the day by cashier (cashier number,
name, total sales).
- **FR-002**: System MUST report sales for the month by cashier (cashier number,
name, total sales).
- **FR-003**: System MUST report over/short for the day by cashier (cashier number,
name, over/short amount).
- **FR-004**: System MUST report over/short for the month by cashier (cashier
number, name, over/short amount).
- **FR-005**: Total sales MUST sum `total` of completed sales for that cashier in
the period.
- **FR-006**: Over/short MUST sum over/short from ended sessions for that cashier
in the period (session end date in period).
- **FR-007**: Only managers may generate cashier reports.
- **FR-008**: Empty periods MUST return empty rows, not an error.
---
## Assumptions
- Features 1, 7, and 8 on `dev`.
- Day/month parameters match Feature 9 (`date` vs `yearMonth`).
---
## Edge Cases
- Cashier with sales but no ended session in period → sales row present; over/short
row may be absent or zero
- Non-manager → `NotAuthorizedException`
- No `AuthSession` → `NotAuthenticatedException`
- Invalid period → `ValidationException`
---
## Success Criteria
- **SC-001**: Every Gherkin scenario has at least one automated JUnit test before
merge
- **SC-002**: Sales-by-cashier totals match fixture completed sales
- **SC-003**: Over/short-by-cashier matches summed session over/short for the
period
- **SC-004**: Cashier role cannot invoke cashier report service methods
---
## Data Ownership & Isolation
- Manager-only store-wide aggregates.
---
## Key Entities
- **Cashier Report Row**: cashier number, name, total sales and/or over/short
- **Sale**, **Sales Session**: source data
---
## Service Requirements
| Service | Method | Auth | Role | Notes |
|---------|--------|------|------|-------|
| `CashierReportService` | `salesByCashierForDay(date) → List<CashierSalesRow>` |
required | `MANAGER` | Total completed sales by cashier for calendar date. |
| `CashierReportService` | `salesByCashierForMonth(yearMonth) →
List<CashierSalesRow>` | required | `MANAGER` | Total completed sales by cashier
for calendar month. |
| `CashierReportService` | `overShortByCashierForDay(date) →
List<CashierOverShortRow>` | required | `MANAGER` | Sum of session over/short by
cashier for calendar date. |
| `CashierReportService` | `overShortByCashierForMonth(yearMonth) →
List<CashierOverShortRow>` | required | `MANAGER` | Sum of session over/short by
cashier for calendar month. |
**`CashierSalesRow`:** `cashierNumber`, `cashierName`, `totalSales`.
**`CashierOverShortRow`:** `cashierNumber`, `cashierName`, `overShort`.
**Errors:** throw `ValidationException`, `NotAuthenticatedException`, or
`NotAuthorizedException` with a human-readable message.
---
## Screen Requirements
| Component | Access |
|-----------|--------|
| `CashierReportPanel` | manager (under manager Reports menu in `MainFrame`) |
- Tabs or toggles: Sales vs Over/Short; Day vs Month; period picker.
- Table columns match FR data elements.
- Empty state when no rows.
**UI diagrams** (from System Design Document):
| Screen | Wireframe |
|--------|-----------|
| Cashier Report | ![Cashier Report](../docs/ui/feature-10-cashier-reports/cashier-
report.png) — [cashier-report.png](../docs/ui/feature-10-cashier-reports/cashier-
report.png) |
---
## Data Model Requirements
No new tables — reads from `sales`, `sales_sessions`, and `cashiers`.
---
## Acceptance Criteria (Gherkin)
### US-10.1 — View sales by cashier for day and month
#### Scenario: Manager views sales for the day by cashier
* **Given** a manager is authorized
* **And** cashiers have completed sales on "2026-07-25"
* **When** the manager generates the day cashier sales report for that date
* **Then** each cashier appears with cashier number, name, and total sales for the
day
#### Scenario: Manager views sales for the month by cashier
* **Given** a manager is authorized
* **And** cashiers have completed sales in "2026-07"
* **When** the manager generates the month cashier sales report for "2026-07"
* **Then** each cashier appears with total sales for the month
### US-10.2 — View over/short by cashier for day and month
#### Scenario: Manager views over and short for the day by cashier
* **Given** cashiers have ended sessions on "2026-07-25" with recorded over/short
* **When** the manager generates the day over/short report for that date
* **Then** each cashier appears with number, name, and over/short amount for the
day
#### Scenario: Manager views over and short for the month by cashier
* **Given** cashiers have ended sessions in "2026-07" with recorded over/short
* **When** the manager generates the month over/short report for "2026-07"
* **Then** each cashier appears with over/short amount for the month
#### Scenario: Cashier cannot generate cashier reports
* **Given** a cashier is authorized
* **When** the cashier invokes a cashier sales report method
* **Then** the system throws `NotAuthorizedException`
---
## Test Coverage Map
| Story | Scenario | Test class | Display name |
|-------|----------|------------|--------------|
| US-10.1 | Manager views sales for the day by cashier |
`app/src/test/java/edu/oc/pos/service/CashierReportServiceTest.java` |
`@DisplayName("Manager views sales for the day by cashier")` |
| US-10.1 | Manager views sales for the month by cashier |
`app/src/test/java/edu/oc/pos/service/CashierReportServiceTest.java` |
`@DisplayName("Manager views sales for the month by cashier")` |
| US-10.2 | Manager views over and short for the day by cashier |
`app/src/test/java/edu/oc/pos/service/CashierReportServiceTest.java` |
`@DisplayName("Manager views over and short for the day by cashier")` |
| US-10.2 | Manager views over and short for the month by cashier |
`app/src/test/java/edu/oc/pos/service/CashierReportServiceTest.java` |
`@DisplayName("Manager views over and short for the month by cashier")` |
| US-10.2 | Cashier cannot generate cashier reports |
`app/src/test/java/edu/oc/pos/service/CashierReportServiceTest.java` |
`@DisplayName("Cashier cannot generate cashier reports")` |
---
## Agent implementation request
Copy when asking Cursor to implement this feature (`@` this file):
```text
Implement Feature 10 from @features/feature-10-cashier-reports.md on branch
`feature/10-cashier-reports`.
Follow layer order in @features/framework.md (domain → repository → service → JUnit
tests → Swing UI).
Map every Gherkin scenario in the Test Coverage Map; run `mvn test` before
finishing.
If service contracts, schema, or product rules changed per this spec, update
@features/reference/services.md, @features/reference/data-model.md, and/or
@features/reference/behavior.md in the same PR to match shipped code.
Complete Definition of Done and the merge checklist in @features/framework.md.
Do not implement behavior not in this spec.
```
**Reference updates for this feature:** `services.md`, `behavior.md`
---
## Definition of Done
* [ ] Domain, services, and Swing UI implemented per this spec (**FR-00N**
satisfied)
* [ ] **Success Criteria (SC-00N)** met
* [ ] All mapped tests pass (`mvn test`)
* [ ] Test Coverage Map complete
* [ ] `features/reference/data-model.md` updated (if schema changed)
* [ ] `features/reference/services.md` updated (if service contracts changed)
* [ ] `features/reference/behavior.md` updated (if product rules changed)
---
## Out of Scope
* Sales by department — Feature 9
* Item reports — Feature 11
* Real-time theft alerts or thresholds
* HTTP/REST API or JWT Bearer tokens

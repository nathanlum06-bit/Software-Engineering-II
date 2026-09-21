# Feature: Sales Reports
**Feature ID:** 9
**Branch pattern:** `feature/9-sales-reports`
**Status:** Ready
**Created:** 2026-07-25
**Input:** Generate sales reports by department for the day and for the month (SRD
FR-9, Use Case Generate Report — Sales).
**Depends on:** [Feature 1 — User Authorization](feature-1-user-authorization.md),
[Feature 8 — Make a Sale](feature-8-make-sale.md)
**Related:** SRD Functional Requirement 9, [ADR-0001](../docs/adr/0001-object-
oriented-java.md), [ADR-0004](../docs/adr/0004-java-swing-ui.md),
[ADR-0005](../docs/adr/0005-junit.md),
[daily-sales-report.png](../docs/ui/feature-9-sales-reports/daily-sales-report.png)
---
## User Stories
### US-9.1: View sales by department for a day
**As a** manager
**I want to** see dollar sales for a selected day grouped by department
**So that** I can see how well the store and departments produced revenue that day
**Priority:** P1
**Independent test:** Call `SalesReportService.byDepartmentForDay(date)`; completed
sales on that date aggregate by item department into dollar amounts
**Acceptance scenarios:** see ### US-9.1 under Acceptance Criteria
### US-9.2: View sales by department for a month
**As a** manager
**I want to** see dollar sales for a selected month grouped by department
**So that** I can see monthly department revenue
**Priority:** P1
**Independent test:** Call `SalesReportService.byDepartmentForMonth(yearMonth)`;
completed sales in the calendar month aggregate by department
**Acceptance scenarios:** see ### US-9.2 under Acceptance Criteria
---
## Requirements
### Functional Requirements
- **FR-001**: System MUST generate a report of sales for a day by department
(department name, dollar amount of items sold).
- **FR-002**: System MUST generate a report of sales for a month by department
(department name, dollar amount of items sold).
- **FR-003**: Dollar amount MUST sum completed sale line totals (not cancelled/in-
progress) for the period.
- **FR-004**: Day report uses a calendar date; month report uses year-month (all
days in that month).
- **FR-005**: Only managers may generate sales reports.
- **FR-006**: Empty periods MUST return an empty list (or zero rows), not an error.
---
## Assumptions
- Features 1 and 8 on `dev`; items have department from Feature 4.
- “Dollar amount of items sold” means sum of line totals (pre-tax merchandise),
unless clarified — use pre-tax line totals consistently and document in
behavior.md.
---
## Edge Cases
- No sales in period → empty report
- Invalid date / month → `ValidationException`
- Non-manager → `NotAuthorizedException`
- No `AuthSession` → `NotAuthenticatedException`
---
## Success Criteria
- **SC-001**: Every Gherkin scenario has at least one automated JUnit test before
merge
- **SC-002**: Day report totals match fixture sales for that date by department
- **SC-003**: Month report includes all days in the month
- **SC-004**: Cashier cannot invoke sales report service methods
---
## Data Ownership & Isolation
- Reports are store-wide aggregates; manager-only.
---
## Key Entities
- **Sales Report Row**: department + dollar amount for a period
- **Sale / Sale Line Item**: source data from Feature 8
- **Item.department**: grouping key from Feature 4
---
## Service Requirements
| Service | Method | Auth | Role | Notes |
|---------|--------|------|------|-------|
| `SalesReportService` | `byDepartmentForDay(date) → List<DepartmentSalesRow>` |
required | `MANAGER` | `date` is a calendar date (`LocalDate`). |
| `SalesReportService` | `byDepartmentForMonth(yearMonth) →
List<DepartmentSalesRow>` | required | `MANAGER` | `yearMonth` is `YYYY-MM` (e.g.
`YearMonth`). |
**`DepartmentSalesRow`:** `department`, `amount`.
**Errors:** throw `ValidationException`, `NotAuthenticatedException`, or
`NotAuthorizedException` with a human-readable message.
---
## Screen Requirements
| Component | Access |
|-----------|--------|
| `SalesReportPanel` | manager (under manager Reports menu in `MainFrame`) |
- Controls to choose Day or Month period and pick date/month; Generate / View
report.
- Table: Department | Dollar Amount.
- Empty state when no rows.
**UI diagrams** (from System Design Document):
| Screen | Wireframe |
|--------|-----------|
| Daily Sales Report | ![Daily Sales
Report](../docs/ui/feature-9-sales-reports/daily-sales-report.png) — [daily-sales-
report.png](../docs/ui/feature-9-sales-reports/daily-sales-report.png) |
| Reports Menu | ![Reports Menu](../docs/ui/menus/reports-menu.png) — [reports-
menu.png](../docs/ui/menus/reports-menu.png) |
---
## Data Model Requirements
No new tables — reads from `sales`, `sale_line_items`, and `items`.
---
## Acceptance Criteria (Gherkin)
### US-9.1 — View sales by department for a day
#### Scenario: Manager views sales for the day by department
* **Given** a manager is authorized
* **And** completed sales exist on date "2026-07-25" for departments "Grocery" and
"Beverage"
* **When** the manager generates the day sales report for "2026-07-25"
* **Then** the report lists each department with the dollar amount of items sold
that day
#### Scenario: Day sales report with no sales is empty
* **Given** a manager is authorized
* **And** no completed sales exist on date "2026-01-01"
* **When** the manager generates the day sales report for "2026-01-01"
* **Then** the report has no department rows
### US-9.2 — View sales by department for a month
#### Scenario: Manager views sales for the month by department
* **Given** a manager is authorized
* **And** completed sales exist in July 2026 across departments
* **When** the manager generates the month sales report for "2026-07"
* **Then** the report lists each department with the dollar amount of items sold
that month
#### Scenario: Cashier cannot generate sales reports
* **Given** a cashier is authorized
* **When** the cashier invokes a sales by department report method
* **Then** the system throws `NotAuthorizedException`
---
## Test Coverage Map
| Story | Scenario | Test class | Display name |
|-------|----------|------------|--------------|
| US-9.1 | Manager views sales for the day by department |
`app/src/test/java/edu/oc/pos/service/SalesReportServiceTest.java` |
`@DisplayName("Manager views sales for the day by department")` |
| US-9.1 | Day sales report with no sales is empty |
`app/src/test/java/edu/oc/pos/service/SalesReportServiceTest.java` |
`@DisplayName("Day sales report with no sales is empty")` |
| US-9.2 | Manager views sales for the month by department |
`app/src/test/java/edu/oc/pos/service/SalesReportServiceTest.java` |
`@DisplayName("Manager views sales for the month by department")` |
| US-9.2 | Cashier cannot generate sales reports |
`app/src/test/java/edu/oc/pos/service/SalesReportServiceTest.java` |
`@DisplayName("Cashier cannot generate sales reports")` |
---
## Agent implementation request
Copy when asking Cursor to implement this feature (`@` this file):
```text
Implement Feature 9 from @features/feature-9-sales-reports.md on branch `feature/9-
sales-reports`.
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
* Cashier reports — Feature 10
* Item reports — Feature 11
* Export to CSV/PDF beyond on-screen report
* Hourly or register-level sales breakdowns
* HTTP/REST API or JWT Bearer tokens

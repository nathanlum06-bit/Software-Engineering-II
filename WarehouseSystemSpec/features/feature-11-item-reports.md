# Feature: Item Reports
**Feature ID:** 11
**Branch pattern:** `feature/11-item-reports`
**Status:** Ready
**Created:** 2026-07-25
**Input:** Generate item sales reports for the day and month (SRD FR-11, Use Case
Generate Report — Items).
**Depends on:** [Feature 1 — User Authorization](feature-1-user-authorization.md),
[Feature 4 — Item Maintenance](feature-4-item-maintenance.md), [Feature 8 — Make a
Sale](feature-8-make-sale.md)
**Related:** SRD Functional Requirement 11, [ADR-0001](../docs/adr/0001-object-
oriented-java.md), [ADR-0004](../docs/adr/0004-java-swing-ui.md),
[ADR-0005](../docs/adr/0005-junit.md), [item-report.png](../docs/ui/feature-11-
item-reports/item-report.png)
---
## User Stories
### US-11.1: View item sales for a day
**As a** manager
**I want to** see quantity and dollar sales by item for a day
**So that** I know what sold best/worst and what to reorder
**Priority:** P1
**Independent test:** Call `ItemReportService.byItemForDay(date)`; aggregate
completed line quantities and amounts by item for the date
**Acceptance scenarios:** see ### US-11.1 under Acceptance Criteria
### US-11.2: View item sales for a month
**As a** manager
**I want to** see quantity and dollar sales by item for a month
**So that** I can manage inventory and assortment over time
**Priority:** P1
**Independent test:** Call `ItemReportService.byItemForMonth(yearMonth)`; aggregate
completed lines by item for the calendar month
**Acceptance scenarios:** see ### US-11.2 under Acceptance Criteria
---
## Requirements
### Functional Requirements
- **FR-001**: System MUST report sales for the day by item: department, item
number, item description, quantity sold, dollar amount sold.
- **FR-002**: System MUST report sales for the month by item with the same columns.
- **FR-003**: Quantity sold MUST sum line quantities from completed sales in the
period.
- **FR-004**: Dollar amount MUST sum line totals from completed sales in the period
(pre-tax, consistent with Feature 9).
- **FR-005**: Only managers may generate item reports.
- **FR-006**: Empty periods MUST return empty rows, not an error.
- **FR-007**: Items with zero sales in the period MUST NOT appear (report sold
items only).
---
## Assumptions
- Features 1, 4, and 8 on `dev`.
- Period parameters match Features 9–10 (`date` vs `yearMonth`).
---
## Edge Cases
- No sales → empty list
- Invalid period → `ValidationException`
- Non-manager → `NotAuthorizedException`
- No `AuthSession` → `NotAuthenticatedException`
---
## Success Criteria
- **SC-001**: Every Gherkin scenario has at least one automated JUnit test before
merge
- **SC-002**: Day item report matches fixture line aggregates
- **SC-003**: Month item report includes all sales in the month
- **SC-004**: Cashier cannot invoke item report service methods
---
## Data Ownership & Isolation
- Manager-only store-wide aggregates.
---
## Key Entities
- **Item Report Row**: department, item number, description, quantity sold, dollar
amount
- **Sale Line Item** + **Item**: source data
---
## Service Requirements
| Service | Method | Auth | Role | Notes |
|---------|--------|------|------|-------|
| `ItemReportService` | `byItemForDay(date) → List<ItemSalesRow>` | required |
`MANAGER` | Sold items for calendar date. |
| `ItemReportService` | `byItemForMonth(yearMonth) → List<ItemSalesRow>` | required
| `MANAGER` | Sold items for calendar month. |
**`ItemSalesRow`:** `department`, `itemNumber`, `description`, `quantitySold`,
`amount`.
**Errors:** throw `ValidationException`, `NotAuthenticatedException`, or
`NotAuthorizedException` with a human-readable message.
---
## Screen Requirements
| Component | Access |
|-----------|--------|
| `ItemReportPanel` | manager (under manager Reports menu in `MainFrame`) |
- Day/Month period picker; report table with FR columns.
- Empty state when no rows.
- Optional sort by quantity or amount (nice-to-have; not required for P1).
**UI diagrams** (from System Design Document):
| Screen | Wireframe |
|--------|-----------|
| Item Report | ![Item Report](../docs/ui/feature-11-item-reports/item-report.png)
— [item-report.png](../docs/ui/feature-11-item-reports/item-report.png) |
---
## Data Model Requirements
No new tables — reads from `sales`, `sale_line_items`, and `items`.
---
## Acceptance Criteria (Gherkin)
### US-11.1 — View item sales for a day
#### Scenario: Manager views sales for the day by item
* **Given** a manager is authorized
* **And** completed sales on "2026-07-25" include lines for multiple items
* **When** the manager generates the day item report for that date
* **Then** each sold item appears with department, item number, description,
quantity sold, and dollar amount
#### Scenario: Day item report with no sales is empty
* **Given** no completed sales on "2026-01-01"
* **When** the manager generates the day item report for that date
* **Then** the report has no item rows
### US-11.2 — View item sales for a month
#### Scenario: Manager views sales for the month by item
* **Given** completed sales exist in "2026-07" for multiple items
* **When** the manager generates the month item report for "2026-07"
* **Then** each sold item appears with quantity sold and dollar amount for the
month
#### Scenario: Cashier cannot generate item reports
* **Given** a cashier is authorized
* **When** the cashier invokes an item report method
* **Then** the system throws `NotAuthorizedException`
---
## Test Coverage Map
| Story | Scenario | Test class | Display name |
|-------|----------|------------|--------------|
| US-11.1 | Manager views sales for the day by item |
`app/src/test/java/edu/oc/pos/service/ItemReportServiceTest.java` |
`@DisplayName("Manager views sales for the day by item")` |
| US-11.1 | Day item report with no sales is empty |
`app/src/test/java/edu/oc/pos/service/ItemReportServiceTest.java` |
`@DisplayName("Day item report with no sales is empty")` |
| US-11.2 | Manager views sales for the month by item |
`app/src/test/java/edu/oc/pos/service/ItemReportServiceTest.java` |
`@DisplayName("Manager views sales for the month by item")` |
| US-11.2 | Cashier cannot generate item reports |
`app/src/test/java/edu/oc/pos/service/ItemReportServiceTest.java` |
`@DisplayName("Cashier cannot generate item reports")` |
---
## Agent implementation request
Copy when asking Cursor to implement this feature (`@` this file):
```text
Implement Feature 11 from @features/feature-11-item-reports.md on branch
`feature/11-item-reports`.
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
* Automatic reordering / purchase orders
* On-hand inventory quantity
* Sales and cashier reports — Features 9–10
* HTTP/REST API or JWT Bearer tokens

# Feature: Item Maintenance
**Feature ID:** 4
**Branch pattern:** `feature/4-item-maintenance`
**Status:** Ready
**Created:** 2026-07-25
**Input:** Maintain items sold in the store — add, update, delete — including
description, department, tax category, UPCs, and prices (SRD FR-1, Use Cases
Add/Update/Delete Item).
**Depends on:** [Feature 1 — User Authorization](feature-1-user-authorization.md),
[Feature 3 — Tax Rate Maintenance](feature-3-tax-rate-maintenance.md) (tax category
must exist)
**Related:** SRD Functional Requirement 1, [ADR-0001](../docs/adr/0001-object-
oriented-java.md), [ADR-0004](../docs/adr/0004-java-swing-ui.md),
[ADR-0005](../docs/adr/0005-junit.md), [item-selection.png](../docs/ui/feature-4-
item-maintenance/item-selection.png), [item-update.png](../docs/ui/feature-4-item-
maintenance/item-update.png)
---
## User Stories
### US-4.1: Add item
**As a** manager
**I want to** add a new item with descriptive data, UPCs, and prices
**So that** cashiers can sell new products with correct identification and pricing
**Priority:** P1
**Independent test:** Manager calls `ItemService.add` with UPC and regular price;
item appears in `ItemService.list`
**Acceptance scenarios:** see ### US-4.1 under Acceptance Criteria
### US-4.2: Update item
**As a** manager
**I want to** update an existing item’s data, UPCs, or prices
**So that** corrections, new barcodes, and price changes are reflected in the
system
**Priority:** P1
**Independent test:** Manager calls `ItemService.update`; `ItemService.get` shows
updated fields; past-effective prices not editable
**Acceptance scenarios:** see ### US-4.2 under Acceptance Criteria
### US-4.3: Delete item
**As a** manager
**I want to** delete an item entered in error
**So that** invalid catalog entries are removed
**Priority:** P1
**Independent test:** Manager calls `ItemService.delete` with no sales history
constraint as specified; item gone from catalog
**Acceptance scenarios:** see ### US-4.3 under Acceptance Criteria
---
## Requirements
### Functional Requirements
- **FR-001**: System MUST allow an authorized manager to add, update, and delete
Items.
- **FR-002**: An Item MUST include: item number, description, department, tax
category, prices, and one or more UPCs.
- **FR-003**: Item number MUST be unique; system MUST NOT add an item if the item
number already exists.
- **FR-004**: Each UPC MUST be unique across the catalog; system MUST NOT add a UPC
that already exists.
- **FR-005**: Regular price MUST include effective date and price.
- **FR-006**: Promotional price MUST include effective date, end date, and price.
- **FR-007**: An item MAY have more than one promotional price.
- **FR-008**: To resolve the selling price for a date, the system MUST use the most
recent effective date on or before the current date that has not passed its end
date (promos with end date; regular prices treated as open-ended unless superseded
by a later regular or applicable promo — see price resolution rule below).
- **FR-009**: System MUST NOT allow update of a price whose effective date is
already in the past.
- **FR-010**: Item number and description are required to save.
- **FR-011**: Tax category on an item MUST reference a known tax category (Feature
3).
- **FR-012**: Only managers may maintain items.
**Price resolution rule (selling price for date D):**
1. Consider all regular and promotional prices for the item where `effectiveDate <=
D`.
2. Exclude promotional prices where `endDate < D` (end date has passed).
3. Among remaining prices, choose the one with the most recent `effectiveDate`.
4. If a promo and a regular share the same effective date, the promotional price
takes precedence while the promo has not ended.
---
## Assumptions
- Feature 1 on `dev`; tax categories exist from Feature 3 (implement Feature 3
before or with Feature 4).
- Department is a string label on the item (no separate department CRUD in this
feature).
- Delete of an item that has been sold may be blocked once Feature 8 exists; until
then, allow delete unless the item has sale line references when that table exists.
- Dates are calendar dates in the store’s local timezone.
---
## Edge Cases
- Duplicate item number on add → `DuplicateException`
- Duplicate UPC on add/update → `DuplicateException`
- Update price with past effective date → `ValidationException`
- Promo end date before effective date → `ValidationException`
- Unknown tax category → `ValidationException`
- Delete unknown item → `NotFoundException`
- Non-manager → `NotAuthorizedException`
---
## Success Criteria
- **SC-001**: Every Gherkin scenario has at least one automated JUnit test before
merge
- **SC-002**: Manager can add an item with UPC and regular price and retrieve it by
item number or UPC via `ItemService.get` / `ItemService.findByUpc`
- **SC-003**: Duplicate item number or UPC throws `DuplicateException`
- **SC-004**: Price with past effective date cannot be updated
(`ValidationException`)
- **SC-005**: `ItemService.resolvePrice` returns the correct promo or regular price
for a given date
---
## Data Ownership & Isolation
- Item catalog is store-wide; only managers mutate it.
- Cashiers read prices via sale flow (Feature 8), not via this maintenance UI.
---
## Key Entities
- **Item**: good for sale; item number (SKU), description, department, tax
category; has many UPCs and prices
- **UPC**: barcode identifier for an item; unique in the system
- **Price**: regular or promotional amount with effective dating (and end date for
promos)
---
## Service Requirements
| Service | Method | Auth | Role | Notes |
|---------|--------|------|------|-------|
| `ItemService` | `list() → List<ItemSummaryView>` | required | MANAGER | Catalog
list |
| `ItemService` | `get(id) → ItemView` | required | MANAGER | Item with UPCs and
prices |
| `ItemService` | `findByUpc(upc) → ItemView` | required | MANAGER / CASHIER | For
sale lookup |
| `ItemService` | `add(itemData) → ItemView` | required | MANAGER | Includes UPCs
and prices |
| `ItemService` | `update(id, itemData) → ItemView` | required | MANAGER | Includes
UPCs and prices |
| `ItemService` | `delete(id)` | required | MANAGER | |
| `ItemService` | `resolvePrice(id, date) → BigDecimal` | required | MANAGER /
CASHIER | Resolved per FR-008 |
**Create/update payload (conceptual):** item fields + collections of `upcs` and
`prices` (`type`: `regular` | `promo`, `effectiveDate`, `endDate` nullable for
regular, `amount`).
**Errors:** throw `ValidationException`, `DuplicateException`, `NotFoundException`,
`NotAuthenticatedException`, or `NotAuthorizedException` with a human-readable
message.
---
## Screen Requirements
| Component | Access |
|-----------|--------|
| `ItemListPanel` | manager |
| `ItemEditDialog` | manager |
- List items in a table; Add Item button opens edit dialog for a new item; Edit
button opens dialog for selected item.
- Form: item number, description, department, tax category (combo), UPC list,
regular/promo price rows with dates.
- Save and Cancel buttons; block save without item number and description.
- Show validation errors for duplicate item number / UPC and past-effective price
edits.
**UI diagrams** (from System Design Document):
| Screen | Wireframe |
|--------|-----------|
| Item Selection | ![Item Selection](../docs/ui/feature-4-item-maintenance/item-
selection.png) — [item-selection.png](../docs/ui/feature-4-item-maintenance/item-
selection.png) |
| Item Update | ![Item Update](../docs/ui/feature-4-item-maintenance/item-
update.png) — [item-update.png](../docs/ui/feature-4-item-maintenance/item-
update.png) |
| UPC Update | ![UPC Update](../docs/ui/feature-4-item-maintenance/upc-update.png)
— [upc-update.png](../docs/ui/feature-4-item-maintenance/upc-update.png) |
| Price Update | ![Price Update](../docs/ui/feature-4-item-maintenance/price-
update.png) — [price-update.png](../docs/ui/feature-4-item-maintenance/price-
update.png) |
---
## Data Model Requirements
### items
| Column | Notes |
|--------|-------|
| id | PK |
| itemNumber | unique, required |
| description | required |
| department | required |
| taxCategory | required (matches tax category name / FK) |
### item_upcs
| Column | Notes |
|--------|-------|
| id | PK |
| itemId | FK |
| upc | unique, required |
### item_prices
| Column | Notes |
|--------|-------|
| id | PK |
| itemId | FK |
| type | `regular` \| `promo` |
| effectiveDate | required |
| endDate | required for promo; null for regular |
| amount | decimal, required |
---
## Acceptance Criteria (Gherkin)
### US-4.1 — Add item
#### Scenario: Manager adds a new item
* **Given** a manager is authorized
* **And** a tax category exists
* **When** the manager enters item number, description, department, tax category,
at least one UPC, and a regular price and saves via `ItemService.add`
* **Then** the item is added to the system
#### Scenario: Manager cannot add item with existing item number
* **Given** an item with item number "1001" exists
* **When** the manager attempts to add another item with item number "1001"
* **Then** the system throws `DuplicateException`
#### Scenario: Manager cannot add UPC that already exists
* **Given** a UPC "012345678905" is already assigned to an item
* **When** the manager attempts to add that UPC to a new or existing item
* **Then** the system throws `DuplicateException`
### US-4.2 — Update item
#### Scenario: Manager updates an existing item
* **Given** a manager is authorized and an item exists
* **When** the manager changes the description and saves via `ItemService.update`
* **Then** the item is updated in the system
#### Scenario: Manager cannot update a price with a past effective date
* **Given** an item has a price whose effective date is before today
* **When** the manager attempts to change that price
* **Then** the system throws `ValidationException`
### US-4.3 — Delete item
#### Scenario: Manager deletes an item
* **Given** a manager is authorized and an item exists that may be deleted
* **When** the manager deletes the item via `ItemService.delete`
* **Then** the item is removed from the system
---
## Test Coverage Map
| Story | Scenario | Test class | Display name |
|-------|----------|------------|--------------|
| US-4.1 | Manager adds a new item |
`app/src/test/java/edu/oc/pos/service/ItemServiceTest.java` |
`@DisplayName("Manager adds a new item")` |
| US-4.1 | Manager cannot add item with existing item number |
`app/src/test/java/edu/oc/pos/service/ItemServiceTest.java` |
`@DisplayName("Manager cannot add item with existing item number")` |
| US-4.1 | Manager cannot add UPC that already exists |
`app/src/test/java/edu/oc/pos/service/ItemServiceTest.java` |
`@DisplayName("Manager cannot add UPC that already exists")` |
| US-4.2 | Manager updates an existing item |
`app/src/test/java/edu/oc/pos/service/ItemServiceTest.java` |
`@DisplayName("Manager updates an existing item")` |
| US-4.2 | Manager cannot update a price with a past effective date |
`app/src/test/java/edu/oc/pos/service/ItemServiceTest.java` |
`@DisplayName("Manager cannot update a price with a past effective date")` |
| US-4.3 | Manager deletes an item |
`app/src/test/java/edu/oc/pos/service/ItemServiceTest.java` |
`@DisplayName("Manager deletes an item")` |
---
## Agent implementation request
Copy when asking Cursor to implement this feature (`@` this file):
```text
Implement Feature 4 from @features/feature-4-item-maintenance.md on branch
`feature/4-item-maintenance`.
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
**Reference updates for this feature:** `services.md`, `data-model.md`,
`behavior.md`
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
* Making a sale / scanning at register — Feature 8
* Tax rate CRUD — Feature 3
* Inventory quantity / reorder levels
* Shelf label printing
* HTTP/REST API

# Feature: Cashier Maintenance
**Feature ID:** 5
**Branch pattern:** `feature/5-cashier-maintenance`
**Status:** Ready
**Created:** 2026-07-25
**Input:** Maintain cashiers who make sales User Authorization]
(feature-1-user-authorization.md)
**Related:** SRD Functional Requirement 3,
[ADR-0001](../docs/adr/0001-object-oriented-java.md), [ADR-0004]
(../docs/adr/0004-java-swing-ui.md), [ADR-0005](../docs/adr/0005-
junit.md), [cashier-selection.png](../docs/ui/feature-5-cashier-
maintenance/cashier-selection.png),
[cashier-update.png](../docs/ui/feature-5-cashier-maintenance/cas
hier-update.png)
---
## User Stories
### US-5.1: Add cashier
**As a** manager
**I want to** add a cashier with number, name, and login
credentials
**So that** the cashier can authenticate and sales can be
attributed to them
**Priority:** P1
**Independent test:** Manager calls `CashierService.add`; unique
number enforced; cashier can log in via `AuthService.login`
**Acceptance scenarios:** see ### US-5.1 under Acceptance
Criteria
### US-5.2: Update cashier
**As a** manager
**I want to** update cashier information
**So that** corrections to name or login details are stored
**Priority:** P1
**Independent test:** Manager calls `CashierService.update`;
`CashierService.get` shows updates; number uniqueness preserved
**Acceptance scenarios:** see ### US-5.2 under Acceptance
Criteria
### US-5.3: Delete cashier
**As a** manager
**I want to** delete a cashier entered in error
**So that** invalid cashiers are removed when they have not made
sales
**Priority:** P1
**Independent test:** `CashierService.delete` succeeds with no
sales; blocked when sales exist
**Acceptance scenarios:** see ### US-5.3 under Acceptance
Criteria
---
## Requirements
### Functional Requirements
- **FR-001**: System MUST allow an authorized manager to add,
update, and delete Cashiers.
- **FR-002**: A Cashier MUST have a cashier number and name
(required).
- **FR-003**: System MUST NOT add a cashier if the cashier number
already exists.
- **FR-004**: System MUST NOT update a cashier to a number that
already belongs to another cashier.
- **FR-005**: System MUST NOT delete a cashier if the cashier has
made sales (once sales exist from Feature 8).
- **FR-006**: Adding a cashier MUST create a linked User with
role `cashier` and login credentials (username and password) so
Feature 1 authentication and Feature 7 sessions work.
- **FR-007**: Only managers may maintain cashiers.
- **FR-008**: Cashier identity is required to track performance
and responsibility for sales and cash drawer activity (Features
7has made sales `DuplicateException`
- Delete cashier with sales `ValidationException`
- Non-manager `NotFoundException`
---
## Success Criteria
- **SC-001**: Every Gherkin scenario has at least one automated
JUnit test before merge
- **SC-002**: Manager can add a cashier and that cashier can log
in with role cashier via `AuthService.login`
- **SC-003**: Duplicate cashier number throws
`DuplicateException`
- **SC-004**: Cashier with sales cannot be deleted
(`ValidationException`; tested with fixture sale when Feature 8
exists; until then unit-level association check)
---
## Data Ownership & Isolation
- Cashiers are store-wide master data; only managers mutate them.
- A cashier must not access another cashier List<CashierView>` |
required | MANAGER | No passwords returned |
| `CashierService` | `get(id) CashierView` | required | MANAGER |
Creates linked User with role CASHIER |
| `CashierService` | `update(id, number, name, username?,
password?) [cashier-selection.png](../docs/ui/feature-5-cashier-
maintenance/cashier-selection.png) |
| Cashier Update | ![Cashier Update](../docs/ui/feature-5-
cashier-maintenance/cashier-update.png) Add cashier
#### Scenario: Manager adds a new cashier
* **Given** a manager is authorized
* **When** the manager enters cashier number, name, and login
credentials and saves via `CashierService.add`
* **Then** the cashier is added to the system
* **And** the cashier can log in with role "cashier" via
`AuthService.login`
#### Scenario: Manager cannot add cashier with existing number
* **Given** a cashier with number "101" exists
* **When** the manager attempts to add another cashier with
number "101"
* **Then** the system throws `DuplicateException`
### US-5.2 Delete cashier
#### Scenario: Manager deletes a cashier with no sales
* **Given** a cashier exists who has not made sales
* **When** the manager deletes the cashier via
`CashierService.delete`
* **Then** the cashier is removed from the system
#### Scenario: Manager cannot delete a cashier who has made sales
* **Given** a cashier has made at least one sale
* **When** the manager attempts to delete that cashier
* **Then** the system throws `ValidationException`
---
## Test Coverage Map
| Story | Scenario | Test class | Display name |
|-------|----------|------------|--------------|
| US-5.1 | Manager adds a new cashier |
`app/src/test/java/edu/oc/pos/service/CashierServiceTest.java` |
`@DisplayName("Manager adds a new cashier")` |
| US-5.1 | Manager cannot add cashier with existing number |
`app/src/test/java/edu/oc/pos/service/CashierServiceTest.java` |
`@DisplayName("Manager cannot add cashier with existing number")`
|
| US-5.2 | Manager updates an existing cashier |
`app/src/test/java/edu/oc/pos/service/CashierServiceTest.java` |
`@DisplayName("Manager updates an existing cashier")` |
| US-5.2 | Manager cannot update cashier to an existing number |
`app/src/test/java/edu/oc/pos/service/CashierServiceTest.java` |
`@DisplayName("Manager cannot update cashier to an existing
number")` |
| US-5.3 | Manager deletes a cashier with no sales |
`app/src/test/java/edu/oc/pos/service/CashierServiceTest.java` |
`@DisplayName("Manager deletes a cashier with no sales")` |
| US-5.3 | Manager cannot delete a cashier who has made sales |
`app/src/test/java/edu/oc/pos/service/CashierServiceTest.java` |
`@DisplayName("Manager cannot delete a cashier who has made
sales")` |
---
## Agent implementation request
Copy when asking Cursor to implement this feature (`@` this
file):
```text
Implement Feature 5 from @features/feature-5-cashier-
maintenance.md on branch `feature/5-cashier-maintenance`.
Follow layer order in @features/framework.md (domain service
Swing UI).
Map every Gherkin scenario in the Test Coverage Map; run `mvn
test` before finishing.
If service contracts, schema, or product rules changed per this
spec, update @features/reference/services.md,
@features/reference/data-model.md, and/or
@features/reference/behavior.md in the same PR to match shipped
code.
Complete Definition of Done and the merge checklist in
@features/framework.md.
Do not implement behavior not in this spec.
```
**Reference updates for this feature:** `services.md`, `data-
model.md`, `behavior.md`
---
## Definition of Done
* [ ] Domain, services, and Swing UI implemented per this spec
(**FR-00N** satisfied)
* [ ] **Success Criteria (SC-00N)** met
* [ ] All mapped tests pass (`mvn test`)
* [ ] Test Coverage Map complete
* [ ] `features/reference/data-model.md` updated (if schema
changed)
* [ ] `features/reference/services.md` updated (if service
contracts changed)
* [ ] `features/reference/behavior.md` updated (if product
rules changed)
---
## Out of Scope
* Starting/ending register sessions Feature 10
* Manager user CRUD (managers remain seeded / out of band)
* HTTP/REST API

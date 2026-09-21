# Feature: Store Maintenance
**Feature ID:** 2
**Branch pattern:** `feature/2-store-maintenance`
**Status:** Ready
**Created:** 2026-07-25
**Input:** Allow a manager to update store number and name for
receipts (SRD FR-4, Use Case Update Store).
**Depends on:** [Feature 1 `ValidationException`
- Non-manager `NotAuthenticatedException`
- `StoreService.get` when no store row exists yet StoreView` |
required | MANAGER / CASHIER | Read store for receipt context |
| `StoreService` | `update(storeNumber, storeName) [store-
maintenance.png](../docs/ui/feature-2-store-maintenance/store-
maintenance.png) |
| Maintenance Menu | ![Maintenance
Menu](../docs/ui/menus/maintenance-menu.png) Update store
information
#### Scenario: Manager updates store information
* **Given** a manager is authorized
* **When** the manager enters store number and store name and
saves
* **Then** the store is updated in the system
* **And** a subsequent `StoreService.get` returns the new number
and name
#### Scenario: Manager cannot save store without required fields
* **Given** a manager is authorized
* **When** the manager attempts to save without a store number or
without a store name
* **Then** the system rejects the save with `ValidationException`
#### Scenario: Cashier cannot update store information
* **Given** a cashier is authorized
* **When** the cashier attempts to update the store via
`StoreService.update`
* **Then** the system throws `NotAuthorizedException`
---
## Test Coverage Map
| Story | Scenario | Test class | Display name |
|-------|----------|------------|--------------|
| US-2.1 | Manager updates store information |
`app/src/test/java/edu/oc/pos/service/StoreServiceTest.java` |
`@DisplayName("Manager updates store information")` |
| US-2.1 | Manager cannot save store without required fields |
`app/src/test/java/edu/oc/pos/service/StoreServiceTest.java` |
`@DisplayName("Manager cannot save store without required
fields")` |
| US-2.1 | Cashier cannot update store information |
`app/src/test/java/edu/oc/pos/service/StoreServiceTest.java` |
`@DisplayName("Cashier cannot update store information")` |
---
## Agent implementation request
Copy when asking Cursor to implement this feature (`@` this
file):
```text
Implement Feature 2 from @features/feature-2-store-maintenance.md
on branch `feature/2-store-maintenance`.
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
* Item, tax, cashier, register maintenance 6
* Printing receipts [Feature 12](feature-12-csv-initial-
import.md)
* Multi-store / add / delete store
* HTTP/REST API

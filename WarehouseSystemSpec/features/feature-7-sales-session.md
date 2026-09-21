# Feature: Sales Session
**Feature ID:** 7
**Branch pattern:** `feature/7-sales-session`
**Status:** Ready
**Created:** 2026-07-25
**Input:** Cashier start and end session on a register, including
starting/ending cash and over/short (SRD FR-7, FR-8, Use Cases
Start/End a Sales Session).
**Depends on:** [Feature 1 Cashier Maintenance](feature-5-
cashier-maintenance.md), [Feature 6 startingCash.
- After Feature 8: expected drawer = startingCash + sum of cash
payment amounts 0 `ValidationException` or `NotFoundException`
- Register already has active session `DuplicateException` or
`ValidationException`
- End with no active session `NotAuthorizedException`
- No `AuthSession` `ValidationException`
---
## Success Criteria
- **SC-001**: Every Gherkin scenario has at least one automated
JUnit test before merge
- **SC-002**: Cashier can start a session with valid register and
starting cash > 0
- **SC-003**: Invalid register or non-positive starting cash is
rejected
- **SC-004**: Ending session returns over/short relative to
starting cash (and sales when present)
---
## Data Ownership & Isolation
- A cashier may only start/end sessions for their own Cashier
identity.
- Managers do not impersonate cashier sessions in this feature.
---
## Key Entities
- **Sales Session**: period a cashier uses a register; holds
starting/ending cash and links to sales
- **Cashier**: employee on the session
- **Register**: device for the session
---
## Service Requirements
| Service | Method | Auth | Role | Notes |
|---------|--------|------|------|-------|
| `SessionService` | `getCurrent() SessionView` | required |
`CASHIER` | Creates session for authenticated cashier and
register. `startingCash` must be > 0. |
| `SessionService` | `end(endingCash)
[start-session.png](../docs/ui/feature-7-sales-session/start-
session.png) |
| End Session | ![End Session](../docs/ui/feature-7-sales-
session/end-session.png) [pos-menu.png](../docs/ui/menus/pos-
menu.png) |
---
## Data Model Requirements
### sales_sessions
| Column | Notes |
|--------|-------|
| id | PK |
| cashierId | FK, required |
| registerId | FK, required |
| startDateTime | required |
| endDateTime | null while active |
| startingCash | decimal, required, > 0 |
| endingCash | decimal, null while active |
| overShort | decimal, set on end (optional cached) |
---
## Acceptance Criteria (Gherkin)
### US-7.1 End a sales session
#### Scenario: Cashier ends a session and sees over and short
* **Given** a cashier is in an active session with starting cash
100.00
* **When** the cashier enters ending cash 105.00 and ends the
session
* **Then** the session is ended
* **And** the system displays over/short of 5.00 (with no sales
yet)
#### Scenario: Cashier cannot end session when none is active
* **Given** a cashier has no active session
* **When** the cashier attempts to end a session
* **Then** the system throws `ValidationException`
---
## Test Coverage Map
| Story | Scenario | Test class | Display name |
|-------|----------|------------|--------------|
| US-7.1 | Cashier starts a session on a register |
`app/src/test/java/edu/oc/pos/service/SessionServiceTest.java` |
`@DisplayName("Cashier starts a session on a register")` |
| US-7.1 | Cashier cannot start session with invalid register or
cash |
`app/src/test/java/edu/oc/pos/service/SessionServiceTest.java` |
`@DisplayName("Cashier cannot start session with invalid register
or cash")` |
| US-7.2 | Cashier ends a session and sees over and short |
`app/src/test/java/edu/oc/pos/service/SessionServiceTest.java` |
`@DisplayName("Cashier ends a session and sees over and short")`
|
| US-7.2 | Cashier cannot end session when none is active |
`app/src/test/java/edu/oc/pos/service/SessionServiceTest.java` |
`@DisplayName("Cashier cannot end session when none is active")`
|
---
## Agent implementation request
Copy when asking Cursor to implement this feature (`@` this
file):
```text
Implement Feature 7 from @features/feature-7-sales-session.md on
branch `feature/7-sales-session`.
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
* Making sales within the session Feature 10
* Training mode sessions (NFR)
* HTTP/REST API or JWT Bearer tokens

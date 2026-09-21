# Feature: User Authorization
**Feature ID:** 1
**Branch pattern:** `feature/1-user-authorization`
**Status:** Ready
**Created:** 2026-07-25
**Input:** Control access to the POS by identity and role;
support log in and log out (SRD FR-12, Use Cases Log in / Log
out).
**Depends on:** s authorized session on log out so that no user
remains authorized in `AppContext`.
- **FR-005**: System MUST deny log out when no user is currently
authorized.
- **FR-006**: System MUST associate each user with a role of
`manager` or `cashier`.
- **FR-007**: System MUST allow manager-role users to access
maintenance and report functions (enforced for Features 211).
- **FR-008**: System MUST deny cashier-role users access to
maintenance and report functions.
- **FR-009**: System MUST require a valid `AuthSession` in
`AppContext` for all protected service methods.
- **FR-010**: An initial manager user MUST be available for
bootstrap (seed or documented setup) so the store can log in
before cashier accounts exist.
---
## Assumptions
- Single password per user is sufficient (SRD); no MFA.
- Username uniquely identifies a user.
- Bootstrap manager is created by seed/script, not by a self-
service registration UI in this feature.
- Cashier user accounts are created when cashiers are maintained
(Feature 5).
- Stand-alone desktop client: one authenticated user per running
JVM ([ADR-0004](../docs/adr/0004-java-swing-ui.md)); no HTTP API
or Bearer tokens.
---
## Edge Cases
- Empty username or password `NotAuthenticatedException` (do not
reveal which field failed)
- Log out when no `AuthSession` `NotAuthorizedException`
---
## Success Criteria
- **SC-001**: Every Gherkin scenario has at least one automated
JUnit test before merge
- **SC-002**: Valid manager and cashier can each complete login
and receive a role in the `UserView`
- **SC-003**: Invalid credentials never establish an
`AuthSession`
- **SC-004**: After logout, protected service methods throw
`NotAuthenticatedException`
- **SC-005**: Role checks throw `NotAuthorizedException` for
cashier on manager-only methods
---
## Data Ownership & Isolation
- `AuthSession` belongs to the authenticated user in this JVM; it
MUST NOT grant another user not an HTTP token
---
## Service Requirements
| Service | Method | Auth | Role | Notes |
|---------|--------|------|------|-------|
| `AuthService` | `login(username, password) | On success, sets
`AuthSession` in `AppContext`. Never returns password hash. |
| `AuthService` | `logout()` | required | concrete panels defined
when those features ship; until then a placeholder panel is
acceptable).
- Log out control on `MainFrame`; clears `AppContext` and returns
to `LoginFrame`.
**UI diagrams** (from System Design Document):
| Screen | Wireframe |
|--------|-----------|
| Main Screen | ![Main Screen](../docs/ui/feature-1-user-
authorization/main-screen.png)
[login-start-session.png](../docs/ui/feature-1-user-
authorization/login-start-session.png) |
| Main Menu | ![Main Menu](../docs/ui/menus/main-menu.png) [pos-
menu.png](../docs/ui/menus/pos-menu.png) |
---
## Data Model Requirements
### users
| Column | Notes |
|--------|-------|
| id | PK |
| username | unique, required |
| password | hashed; never exposed outside security package |
| role | enum: `MANAGER` \| `CASHIER`, required |
| cashierId | nullable FK to cashiers (set in Feature 5); null
for managers |
No persistent auth-token table is required; `AuthSession` is in-
memory per [auth-patterns.mdc](../.cursor/rules/auth-
patterns.mdc).
---
## Acceptance Criteria (Gherkin)
### US-1.1 Log out
#### Scenario: Authorized user logs out
* **Given** a user is currently authorized
* **When** the user selects log out
* **Then** the system ends the session
* **And** no user remains authorized in `AppContext`
#### Scenario: Log out when no user is authorized
* **Given** no user is currently authorized
* **When** the client attempts to log out
* **Then** the system rejects the request with
`NotAuthenticatedException`
### US-1.3 s own rules)
#### Scenario: Cashier is denied a management function
* **Given** a user is logged in with role "cashier"
* **When** the user invokes a manager-only protected service
method
* **Then** the system throws `NotAuthorizedException`
---
## Test Coverage Map
| Story | Scenario | Test class | Display name |
|-------|----------|------------|--------------|
| US-1.1 | User logs in with valid credentials |
`app/src/test/java/edu/oc/pos/security/AuthServiceTest.java` |
`@DisplayName("User logs in with valid credentials")` |
| US-1.1 | User logs in with invalid credentials |
`app/src/test/java/edu/oc/pos/security/AuthServiceTest.java` |
`@DisplayName("User logs in with invalid credentials")` |
| US-1.1 | User logs in when not set up in the system |
`app/src/test/java/edu/oc/pos/security/AuthServiceTest.java` |
`@DisplayName("User logs in when not set up in the system")` |
| US-1.2 | Authorized user logs out |
`app/src/test/java/edu/oc/pos/security/AuthServiceTest.java` |
`@DisplayName("Authorized user logs out")` |
| US-1.2 | Log out when no user is authorized |
`app/src/test/java/edu/oc/pos/security/AuthServiceTest.java` |
`@DisplayName("Log out when no user is authorized")` |
| US-1.3 | Manager accesses a management function |
`app/src/test/java/edu/oc/pos/security/AuthServiceTest.java` |
`@DisplayName("Manager accesses a management function")` |
| US-1.3 | Cashier is denied a management function |
`app/src/test/java/edu/oc/pos/security/AuthServiceTest.java` |
`@DisplayName("Cashier is denied a management function")` |
---
## Agent implementation request
Copy when asking Cursor to implement this feature (`@` this
file):
```text
Implement Feature 1 from @features/feature-1-user-
authorization.md on branch `feature/1-user-authorization`.
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
* Maintaining store, items, tax rates, cashiers, registers 6
* Sales sessions and making sales 8
* Reports 11
* Self-service user registration UI
* MFA, password reset, and password complexity policy beyond
hashed storage
* HTTP/REST API or JWT Bearer tokens

# Feature: Make a Sale
**Feature ID:** 8
**Branch pattern:** `feature/8-make-sale`
**Status:** Ready
**Created:** 2026-07-25
**Input:** Record a sale Store Maintenance](feature-2-store-
maintenance.md), [Feature 3 Item Maintenance](feature-4-item-
maintenance.md), [Feature 7 total and returns `ReceiptView` with
receipt data
**Acceptance scenarios:** see ### US-8.4 under Acceptance
Criteria
---
## Requirements
### Functional Requirements
- **FR-001**: System MUST record a sale transaction with sales
date/time, tax-free flag, cashier, register, line items, and
payments.
- **FR-002**: A sale MAY only be started when the cashier has an
active sales session (Feature 7).
- **FR-003**: Adding a line MUST look up the item (by UPC or item
number), resolve price for the current date per Feature 4 price
rules, and record item, quantity, unit price, and line total.
- **FR-004**: System MUST reject add when the item/UPC is not in
the system.
- **FR-005**: Deleting a line MUST remove it from the sale and
recalculate subtotal, tax, and total; item must already be on the
sale.
- **FR-006**: System MUST calculate tax using the item total.
- **FR-011**: Deleting a payment MUST reverse that payment
(record reversal or remove unpaid tender) so totals due update.
- **FR-012**: Cancel sale MUST remove the in-progress sale as if
it did not happen (no completed sale record).
- **FR-013**: End sale MUST require sum of payments document in
behavior.md).
**Payment data elements:**
| Type | Fields |
|------|--------|
| Cash | amount |
| Check | amount, routingNumber, accountNumber, authorizationCode
|
| Card | amount, authorizationCode, type, number, expireDate |
---
## Assumptions
- Features 2, 3, 4, and 7 on `dev`.
- One in-progress sale per active session.
- Receipt is a `ReceiptView` shown in a print dialog or printable
Swing panel ([ADR-0004](../docs/adr/0004-java-swing-ui.md)).
- External payment gateway is stubbed via cashier-entered
authorization codes.
---
## Edge Cases
- Add unknown UPC `ValidationException`
- End sale with payments < total `ValidationException`
- No active session `NotAuthenticatedException`
- Non-cashier role `ValidationException`
- Quantity `ValidationException`
---
## Success Criteria
- **SC-001**: Every Gherkin scenario has at least one automated
JUnit test before merge
- **SC-002**: Adding a known item applies the correct price for
today and updates subtotal
- **SC-003**: Tax matches category rate unless tax-free
- **SC-004**: Mixed payments can cover a sale; change is correct
for over-tender cash
- **SC-005**: Cancel leaves no completed sale; end persists sale
only when paid in full
---
## Data Ownership & Isolation
- Sales belong to the cashier and register of the active session.
- Cashiers must not mutate another cashier SaleView` | required |
`CASHIER` | Starts in-progress sale for active session. |
| `SaleService` | `getCurrent() SaleView` | required | `CASHIER`
| Adds line; resolves price for current date. |
| `SaleService` | `removeItem(lineId) SaleView` | required |
`CASHIER` | Sets tax-free flag on current sale. |
| `SaleService` | `addPayment(payment) SaleView` | required |
`CASHIER` | Reverses or removes payment. |
| `SaleService` | `cancel()` | required | `CASHIER` | Discards
in-progress sale. |
| `SaleService` | `end() [sale-entry.png](../docs/ui/feature-8-
make-sale/sale-entry.png) |
| Payment Entry | ![Payment Entry](../docs/ui/feature-8-make-
sale/payment-entry.png)
[cash-payment-entry.png](../docs/ui/feature-8-make-sale/cash-
payment-entry.png) |
| Check Payment | ![Check Payment](../docs/ui/feature-8-make-
sale/check-payment-entry.png)
[credit-payment-entry.png](../docs/ui/feature-8-make-sale/credit-
payment-entry.png) |
---
## Data Model Requirements
### sales
| Column | Notes |
|--------|-------|
| id | PK |
| sessionId | FK |
| cashierId | FK |
| registerId | FK |
| saleDateTime | required |
| taxFree | boolean, default false |
| subtotal, tax, total | decimals |
| status | `in_progress` \| `completed` \| `cancelled` |
### sale_line_items
| Column | Notes |
|--------|-------|
| id | PK |
| saleId | FK |
| itemId | FK |
| quantity | required |
| unitPrice | required |
| lineTotal | required |
### sale_payments
| Column | Notes |
|--------|-------|
| id | PK |
| saleId | FK |
| type | cash \| check \| card |
| amount | required |
| routingNumber, accountNumber | check |
| authorizationCode | check/card |
| cardType, cardNumber, expireDate | card |
| isReversal | boolean optional |
---
## Acceptance Criteria (Gherkin)
### US-8.1 Calculate sale totals and tax
#### Scenario: System calculates tax and total for a sale
* **Given** a sale has line items in a taxed category with a
known rate for today
* **When** totals are calculated
* **Then** tax equals the taxable subtotal times the applicable
rate
* **And** total equals subtotal plus tax
#### Scenario: Tax-free sale has zero tax
* **Given** a sale is marked tax-free
* **When** totals are calculated
* **Then** tax is 0
* **And** total equals subtotal
### US-8.3 Cancel or end sale
#### Scenario: Cashier cancels a sale
* **Given** a cashier is making a sale
* **When** the cashier cancels the sale
* **Then** the sale is removed as if it did not happen
#### Scenario: Cashier ends a fully paid sale
* **Given** a sale has payments greater than or equal to the sale
total
* **When** the cashier ends the sale
* **Then** the system saves the sale transaction
* **And** the system provides a receipt including store
information
#### Scenario: Cashier cannot end sale when underpaid
* **Given** payments are less than the sale total
* **When** the cashier attempts to end the sale
* **Then** the system does not complete the sale
* **And** the system throws `ValidationException`
---
## Test Coverage Map
| Story | Scenario | Test class | Display name |
|-------|----------|------------|--------------|
| US-8.1 | Cashier adds an item to a sale |
`app/src/test/java/edu/oc/pos/service/SaleServiceTest.java` |
`@DisplayName("Cashier adds an item to a sale")` |
| US-8.1 | Cashier cannot add an unknown item |
`app/src/test/java/edu/oc/pos/service/SaleServiceTest.java` |
`@DisplayName("Cashier cannot add an unknown item")` |
| US-8.1 | Cashier deletes an item from the sale |
`app/src/test/java/edu/oc/pos/service/SaleServiceTest.java` |
`@DisplayName("Cashier deletes an item from the sale")` |
| US-8.2 | System calculates tax and total for a sale |
`app/src/test/java/edu/oc/pos/service/SaleServiceTest.java` |
`@DisplayName("System calculates tax and total for a sale")` |
| US-8.2 | Tax-free sale has zero tax |
`app/src/test/java/edu/oc/pos/service/SaleServiceTest.java` |
`@DisplayName("Tax-free sale has zero tax")` |
| US-8.3 | Cashier adds a cash payment and sees change |
`app/src/test/java/edu/oc/pos/service/SaleServiceTest.java` |
`@DisplayName("Cashier adds a cash payment and sees change")` |
| US-8.3 | Cashier adds a check payment |
`app/src/test/java/edu/oc/pos/service/SaleServiceTest.java` |
`@DisplayName("Cashier adds a check payment")` |
| US-8.3 | Cashier adds a card payment |
`app/src/test/java/edu/oc/pos/service/SaleServiceTest.java` |
`@DisplayName("Cashier adds a card payment")` |
| US-8.3 | Cashier deletes a payment |
`app/src/test/java/edu/oc/pos/service/SaleServiceTest.java` |
`@DisplayName("Cashier deletes a payment")` |
| US-8.4 | Cashier cancels a sale |
`app/src/test/java/edu/oc/pos/service/SaleServiceTest.java` |
`@DisplayName("Cashier cancels a sale")` |
| US-8.4 | Cashier ends a fully paid sale |
`app/src/test/java/edu/oc/pos/service/SaleServiceTest.java` |
`@DisplayName("Cashier ends a fully paid sale")` |
| US-8.4 | Cashier cannot end sale when underpaid |
`app/src/test/java/edu/oc/pos/service/SaleServiceTest.java` |
`@DisplayName("Cashier cannot end sale when underpaid")` |
---
## Agent implementation request
Copy when asking Cursor to implement this feature (`@` this
file):
```text
Implement Feature 8 from @features/feature-8-make-sale.md on
branch `feature/8-make-sale`.
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
* Live credit/check authorization vendor integration (stub
authorization codes only)
* PCI DSS certification / tokenization service
* Training mode that does not log transactions (NFR)
* Sales / cashier / item reports 11
* Returns / voids after sale is completed
* HTTP/REST API or JWT Bearer tokens

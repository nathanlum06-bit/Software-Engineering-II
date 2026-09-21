# Feature: CSV Initial Import
**Feature ID:** 12
**Branch pattern:** `feature/12-csv-initial-import`
**Status:** Ready
**Created:** 2026-07-25
**Input:** Allow initial load of POS data from a CSV file (store,
tax categories, items, registers, cashiers; optional sample
sessions/sales) per SDD External Interface User Authorization]
(feature-1-user-authorization.md); entity shapes from [Feature 2]
(feature-2-store-maintenance.md)[Feature 8](feature-8-make-
sale.md) if importing sample sales)
**Related:** SDD External Interface Specifications (CSV File
Import), [ADR-0001](../docs/adr/0001-object-oriented-java.md),
[ADR-0003](../docs/adr/0003-jpa-orm.md),
[ADR-0004](../docs/adr/0004-java-swing-ui.md),
[ADR-0005](../docs/adr/0005-junit.md)
---
## User Stories
### US-12.1: Import master data from CSV
**As a** manager
**I want to** import initial store, tax, item, register, and
cashier data from a CSV file
**So that** the POS can be bootstrapped without re-entering every
record by hand
**Priority:** P1
**Independent test:** Manager calls
`CsvImportService.importFile(path)` with a fixture CSV containing
Store, TaxCategory, Item, Register, and Cashier rows; subsequent
service gets return the imported data
**Acceptance scenarios:** see ### US-12.1 under Acceptance
Criteria
### US-12.2: Reject invalid or unauthorized import
**As a** manager
**I want** bad rows and unauthorized callers rejected clearly
**So that** the database is not left half-imported with silent
corruption
**Priority:** P1
**Independent test:** Invalid row `NotAuthorizedException`;
import rolls back on failure
**Acceptance scenarios:** see ### US-12.2 under Acceptance
Criteria
### US-12.3: Optional sample session and sale rows
**As a** manager
**I want** optional Session / Sale / SaleLineItem / Payment rows
in the same CSV format
**So that** lab demos can load sample history after master data
**Priority:** P2
**Independent test:** Fixture with sample Sale rows imports only
when master keys resolve; otherwise `ValidationException`
**Acceptance scenarios:** see ### US-12.3 under Acceptance
Criteria
---
## Requirements
### Functional Requirements
- **FR-001**: System MUST allow an authorized manager to import
initial data from a UTF-8 CSV file chosen via file dialog (or
path passed to the service for tests).
- **FR-002**: Each data row MUST begin with a **class name**
token, then the fields for that class (SDD CSV File Import).
Supported class names and fields:
| Class name (CSV) | Fields (order) | Maps to |
|------------------|----------------|---------|
| `Store` | Name | Store name (SDD). Store number: use existing
single-store row number if present, else require a prior seed /
Feature 2 default before import |
| `TaxCategory` | Category, Rate, Effective Date | Tax category +
rate (Feature 3) |
| `Item` | Number, Description, UPC, Price | Item + UPC + initial
price (Feature 4) |
| `Register` | Number | Register (Feature 6) |
| `Cashier` | Number, Name, SSN, Address, City, State, Zip,
Phone, Password | Cashier (Feature 5); password stored hashed per
Feature 1/5 |
| `Session` | Register Number, Cashier Number | Sales session
(Feature 7) P2 |
| `SaleLineItem` | Item Number, Quantity | Line on preceding Sale
P2 |
- **FR-003**: Import MUST run in a **single transaction**: any
validation or persistence failure rolls back the entire file.
- **FR-004**: Unauthenticated or non-manager callers MUST NOT
import (`NotAuthenticatedException` / `NotAuthorizedException`).
- **FR-005**: On success, the service MUST return a summary:
counts imported per class name.
- **FR-006**: Passwords in Cashier rows MUST NOT be logged in
plain text.
- **FR-007**: Re-import of the same natural keys (item number,
register number, cashier number, etc.) MUST fail with
`DuplicateException` or `ValidationException` (no silent
overwrite) unless a future feature explicitly authorizes upsert.
- **FR-008**: CSV parsing and file I/O belong in
`edu.oc.pos.systeminterface` (C4 System Interface); orchestration
and persistence rules belong in `CsvImportService`.
---
## Assumptions
- Feature 1 auth and manager role checks are available.
- Domain entities/tables for Features 2 `ValidationException`
- Unknown class name token `ValidationException` citing row
number
- Tax/Item/Cashier/Register references in P2 Session/Sale rows
that do not exist `ValidationException` (or `IOException` wrapped
with a human-readable message)
- Cashier import attempt `DuplicateException` /
`ValidationException`
---
## Success Criteria
- **SC-001**: Every Gherkin scenario has at least one automated
JUnit test before merge
- **SC-002**: Manager can import a fixture CSV of Store +
TaxCategory + Item + Register + Cashier and read them back via
the corresponding services
- **SC-003**: Failed row causes zero committed changes from that
import attempt
- **SC-004**: Cashier cannot call `CsvImportService.importFile`
---
## Data Ownership & Isolation
- Import is a manager-only bootstrap/maintenance operation;
cashiers have no access.
---
## Key Entities
- **CsvImportJob** (conceptual): path, actor, per-class counts,
success/failure
- Existing aggregates: Store, TaxCategory/TaxRate, Item,
Register, Cashier; optionally Session, Sale, SaleLineItem,
Payment
---
## Service Requirements
| Service | Method | Auth | Role | Notes |
|---------|--------|------|------|-------|
| `CsvImportService` | `importFile(Path path) ImportSummary` |
required | MANAGER | Optional: `includeTransactionalRows` default
true for P2 rows when present |
**`ImportSummary`:** map or fields of counts per class name;
total rows committed.
**Errors:** throw `ValidationException`, `DuplicateException`,
`NotAuthenticatedException`, or `NotAuthorizedException` with a
human-readable message (see [service-conventions.mdc]
(../.cursor/rules/service-conventions.mdc)).
---
## Screen Requirements
| Component | Access |
|-----------|--------|
| `CsvImportPanel` or `CsvImportDialog` | manager |
- File chooser for `.csv`; Import button; busy state while
running (`SwingWorker`); success dialog showing `ImportSummary`;
error dialog with message.
- Entry point: Maintenance menu (or File manager only.
**UI diagrams:** none in SDD for this screen; follow [swing-
ui.mdc](../.cursor/rules/swing-ui.mdc) and maintenance patterns
from Features 26 (and 7 Import master data from CSV
#### Scenario: Manager imports master data from CSV
* **Given** a manager is authorized
* **And** a CSV file contains valid Store, TaxCategory, Item,
Register, and Cashier rows
* **When** the manager imports the file via
`CsvImportService.importFile`
* **Then** the import succeeds with non-zero counts for those
classes
* **And** subsequent gets for store, tax, item, register, and
cashier reflect the imported values
#### Scenario: Store name from CSV is available after import
* **Given** a manager is authorized
* **And** a CSV contains a Store row with name "Main Street
Market"
* **When** the manager imports the file
* **Then** `StoreService.get` returns store name "Main Street
Market"
### US-12.2 Optional sample session and sale rows
#### Scenario: Manager imports sample sale rows after master data
* **Given** a manager is authorized
* **And** a CSV contains master data plus Session, Sale,
SaleLineItem, and Payment rows with valid references
* **When** the manager imports the file
* **Then** the import summary includes counts for those
transactional classes
* **And** the sample sale is readable via report or sale query
APIs used by Features 8 repository service Swing UI).
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
**Reference updates for this feature:** `services.md`,
`behavior.md`
---
## Definition of Done
* [ ] Domain, system-interface adapter, services, and Swing UI
implemented per this spec (**FR-00N** satisfied)
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
* Ongoing EDI / nightly sync feeds
* Excel / JSON import formats
* Silent upsert / merge of existing natural keys
* HTTP file upload API
* Training-mode-only import (see Trainability NFR)

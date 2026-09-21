# Feature: Tax Rate Maintenance
**Feature ID:** 3
**Branch pattern:** `feature/3-tax-rate-maintenance`
**Status:** Ready
**Created:** 2026-07-25
**Input:** Maintain sales tax rates by tax category and effective
date (SRD FR-2, Use Cases Add/Update/Delete Tax Rate).
**Depends on:** [Feature 1 prefer percent points stored as
decimal fraction.
- Feature 4 depends on tax categories existing; ship Feature 3
before Feature 4, or seed categories in tests.
---
## Edge Cases
- Duplicate category + effective date today
`ValidationException`
- Non-manager `NotFoundException`
---
## Success Criteria
- **SC-001**: Every Gherkin scenario has at least one automated
JUnit test before merge
- **SC-002**: Manager can add a tax rate and retrieve the
applicable rate for a category on a date via
`TaxRateService.getCurrentRate`
- **SC-003**: Duplicate category+date throws `DuplicateException`
- **SC-004**: Tax rate with past effective date cannot be deleted
(`ValidationException`)
---
## Data Ownership & Isolation
- Tax rates are store-wide; only managers mutate them.
---
## Key Entities
- **Tax Rate**: rate charged for a tax category starting on an
effective date
- **Tax Category**: named classification of items for tax (e.g.
grocery, prepared food)
---
## Service Requirements
| Service | Method | Auth | Role | Notes |
|---------|--------|------|------|-------|
| `TaxRateService` | `list() TaxRateView` | required | MANAGER |
Single tax rate |
| `TaxRateService` | `getCurrentRate(category, date) TaxRateView`
| required | MANAGER | Rejects duplicate category+date |
| `TaxRateService` | `update(id, taxCategory, effectiveDate,
rate) today |
**`TaxRateView`:** `id`, `taxCategory`, `effectiveDate`, `rate`.
**Errors:** throw `ValidationException`, `DuplicateException`,
`NotFoundException`, `NotAuthenticatedException`, or
`NotAuthorizedException` with a human-readable message.
---
## Screen Requirements
| Component | Access |
|-----------|--------|
| `TaxRateListPanel` | manager |
| `TaxRateEditDialog` | manager |
- List tax rates in a table; Add, Edit, and Delete buttons.
- Edit dialog: tax category, effective date, and rate fields;
Save and Cancel buttons.
- Require tax category, effective date, and rate before save.
- Disable or show error on delete when effective date has passed.
**UI diagrams** (from System Design Document):
| Screen | Wireframe |
|--------|-----------|
| Tax Category Selection | ![Tax Category
Selection](../docs/ui/feature-3-tax-rate-maintenance/tax-
category-selection.png)

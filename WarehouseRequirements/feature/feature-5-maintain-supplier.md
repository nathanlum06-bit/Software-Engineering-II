# Feature: Supplier Maintenance

**Feature ID:** 5

**Branch pattern:** `feature/feature-5-maintain-supplier`

**Status:** Draft

**Created:** 2026-09-23

**Input:** Maintain supplier information - Add, Update, Delete - including Supplier ID, Supplier Name, Address, Phone Number, Email, Website, Contact Name, Business Hours, Ship Days, Terms, Min Order

**Depends On:**
**Related:** feature-4-maintain-product, feature-7-maintain-supplier-orderForm
---

## User Stories

### US-5.1: Create Supplier Information

**As a** Company Admin

**I want to** add a supplier's information

**So that** the company knows who to order products from

### US-5.2: Update Supplier Information

**As a** Company Admin

**I want to** update information about our suppliers

**So that** the supplier's information remains accurate and up to date

### US-5.3: Delete Supplier Information

**As a** Company Admin

**I want to** delete suppliers that the company no longer orders from

**So that** the supplier's information remains accurate and up to date

---

## Functional Requirements (Rules)

- **FR-001:** System MUST maintain information for each supplier.
- **FR-002:** System MUST allow an authorized admin to add, update, delete information about each supplier
- **FR-003:** Supplier MUST include: Supplier ID, Supplier Name, Address, Phone Number, Email, Website, Contact Name, Business Hours, Ship Days, Terms, Min Order
- **FR-004:** System MUST save information about the supplier
- **FR-005:** System MUST display supplier information
- **FR-006:** System MUST check that the information is correct and acceptable before allowing it to be saved (Validation)
- **FR-007:** System MUST only allow authorized users to maintain supplier information 
- **FR-008:** System MUST display error messages if required input fields are invalid or empty 
- **FR-009:** System MUST prevent a supplier from being deleted while a product or supplier order is associated with it
- **FR-010:** System MUST provide confirmation before deleting a supplier
---

## Key Entities

- **Supplier:** Represents a company or business that provides products to our warehouse company. Includes Supplier ID, Supplier Name, Address, Phone Number, Email, Website, Contact Name, Business Hours, Ship Days, Terms, and Min Order.

  - **Ship Days:** The number of days required for the supplier to ship an order after it is placed.
  - **Terms:** The conditions or requirements established by the supplier for placing and receiving orders.
  - **Min Order:** The minimum quantity or value that must be ordered from the supplier.

- **Supplier Order:** Represents an order placed by the warehouse company with a supplier.

 **Product:** Represents a product supplied by the supplier and sold by our company.

---

## Initial Data Model

| Entity | Attribute | Type | Constraints / Notes |
|---|---|---|---|
| Supplier | SupplierID | Integer | Primary Key, Unique Identifier, Required |
| Supplier | SupplierName | String | Required |
| Supplier | Address | String | Required |
| Supplier | PhoneNumber | String | Required |
| Supplier | Email | String | Required, Valid Email Format |
| Supplier | Website | String | Optional, Valid URL Format |
| Supplier | ContactName | String | Required |
| Supplier | BusinessHours | String | Required |
| Supplier | ShipDays | Integer | Required, Number of days required for supplier delivery |
| Supplier | Terms | String | Required, Defines the supplier's ordering terms |
| Supplier | MinOrder | Decimal | Required, Minimum order requirement |

### Associations

- **Supplier &rarr; Product**: A supplier can provide multiple products, and each product is associated with a supplier through Supplier ID
---

## Gherkin Acceptance Criteria

### US-5.1: Add Supplier

#### Scenario: Add new supplier information (happy path)

- **Given** a supplier is now providing products for the company
- **And** the user is an authorized Company Admin
- **When** the Admin enters the new supplier information
- **And** submits the new supplier information
- **Then** the system validates the information
- **And** the system saves the supplier information
- **And** the system displays the new supplier  information

#### Scenario: Add invalid supplier information (failure / edge)

- **Given** a supplier is now providing products for the company
- **And** the user is an authorized Company Admin
- **When** the Admin enters invalid supplier information
- **And** submits the invalid supplier information
- **Then** the system displays an error message
- **And** the invalid information is not saved
- **And** the system requires the user to correct the invalid information before submitting again


### US-5.2: Update Supplier

#### Scenario: Update supplier information (happy path)

- **Given** existing supplier information is outdated or needs to be changed
- **And** the user is an authorized Company Admin
- **When** the Admin changes the existing supplier information
- **And** submits the changes
- **Then** the system validates the information
- **And** the system saves the updated supplier information
- **And** the system displays the updated supplier information

#### Scenario: Update supplier with invalid information (failure / edge)

- **Given** existing supplier information is outdated or needs to be changed
- **And** the user is an authorized Company Admin
- **When** the Admin enters invalid supplier information
- **And** submits the changes
- **Then** the system displays an error message
- **And** the invalid information is not saved
- **And** the system requires the user to correct the invalid information before submitting again

### US-5.3: Deleting Supplier 

#### Scenario: Delete supplier information (happy path)

- **Given** a supplier has no products or supplier orders associated with it
- **And** the user is an authorized Company Admin
- **When** the Admin selects the supplier to delete
- **And** confirms the deletion
- **Then** the system deletes the supplier
- **And** the supplier is no longer displayed in the system

#### Scenario: Cannot delete supplier with products or orders associated (failure / edge)

- **Given** a supplier has products or supplier orders associated with it
- **And** the user is an authorized Company Admin
- **When** the Admin attempts to delete the supplier
- **Then** the system displays an error message
- **And** the supplier is not deleted
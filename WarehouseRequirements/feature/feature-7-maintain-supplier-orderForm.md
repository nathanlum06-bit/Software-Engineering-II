# Feature: Supplier Order Form Maintenance

**Feature ID:** 7

**Branch pattern:** `feature/feature-7-maintain-supplier-orderForm`

**Status:** Draft

**Created:** 2026-09-23

**Input:** Maintain supplier order information - Add, Update, Delete - including Ordered By (Company ID), Ordered From (Supplier ID), Customer Number, Order Date, P.O Number, SKU, Description, Cases, Price, Product Total, Order Total, Authorized By, Warehouse ID

**Depends On:** feature-1-maintain-company, feature-2-maintain-warehouse, feature-4-maintain-product, feature-5-maintain-supplier

**Related:** feature-8-maintain-customer-orderForm

---

## User Stories

### US-7.1: Create Supplier Order

**As a** Company Admin

**I want to** create an order form for a supplier

**So that** the company can restock products on pallets

### US-7.2: Update Supplier Order

**As a** Company Admin

**I want to** update information about our order

**So that** the order's information remains accurate and up to date

### US-7.3: Delete Supplier Order

**As a** Company Admin

**I want to** delete an order that the company no longer needs

**So that** the order's information remains accurate and up to date

---

## Functional Requirements (Rules)

- **FR-001:** System MUST maintain information for each order for a supplier
- **FR-002:** System MUST allow an authorized admin to add, update, delete information about each order
- **FR-003:** Supplier Order MUST include Company ID, Supplier ID, Customer Number, Order Date, P.O. Number, Authorized By, Order Total, and Warehouse ID. 
- **FR-004:** Each Supplier Order Line MUST include SKU, Description, Cases, Price, and Product Total.
- **FR-005:** System MUST save information about the order
- **FR-006:** System MUST display order information
- **FR-007:** System MUST check that the information is correct and acceptable before allowing it to be saved (Validation)
- **FR-008:** System MUST only allow authorized users to maintain order information 
- **FR-009:** System MUST display error messages if required input fields are invalid or empty 
- **FR-010:** System MUST prevent a supplier order from being deleted after it has been submitted to the supplier
- **FR-011:** System MUST provide confirmation before deleting an order
---

## Key Entities

- **Supplier Order:** the entire order placed by the warehouse company with a supplier. Contains information that applies to the overall order, such as Order ID, supplier, order date, P.O. number, authorized person, and receiving warehouse.

- **Supplier Order Line:** one product within a supplier order. Contains the product's SKU, description, number of cases, price, and product total. An order can contain multiple supplier order lines.

- **Supplier:** Represents a company or business that provides products to our warehouse company.

- **Product:** Represents a specific product that the company sells. Includes Product ID, Product Name, SKU, Product UPC, Case UPC, Price, Supplier ID, and Description
  - **SKU:** A company-assigned identifier used to track and manage a specific product internally
---

## Initial Data Model

| Entity | Attribute | Type | Constraints / Notes |
|---|---|---|---|
| SupplierOrder | OrderID | Integer | Primary Key, Unique Identifier, Required |
| SupplierOrder | CompanyID | Integer | Foreign Key, Required, Identifies the company placing the order |
| SupplierOrder | SupplierID | Integer | Foreign Key, Required, Identifies the supplier receiving the order |
| SupplierOrder | CustomerNumber | Integer | Required, Identifies the company's customer/account number with the supplier |
| SupplierOrder | OrderDate | Date | Required |
| SupplierOrder | PONumber | String | Required, Unique Identifier |
| SupplierOrder | AuthorizedBy | String | Required, Identifies the person who authorized the order |
| SupplierOrder | OrderTotal | Decimal | Required, Calculated from the total of all ProductTotal values |
| SupplierOrder | WarehouseID | Integer | Foreign Key, Required, Identifies the warehouse receiving the order |
| SupplierOrderLine | OrderID | Integer | Foreign Key, Required, References SupplierOrder |
| SupplierOrderLine | SKU | String | Foreign Key, Required, References Product SKU |
| SupplierOrderLine | Description | String | Required, Describes the product |
| SupplierOrderLine | Cases | Integer | Required, Must be 0 or greater |
| SupplierOrderLine | Price | Decimal | Required, Price for the order line |
| SupplierOrderLine | ProductTotal | Decimal | Required, Calculated from Cases × Price |

### Associations

- **Supplier &rarr; Supplier Order:** A supplier can receive multiple supplier orders, and each supplier order is associated with one supplier through Supplier ID

- **Supplier Order &rarr; Supplier Order Line:** A supplier order can contain multiple supplier order lines, and each supplier order line is associated with one supplier order through Order ID

- **Supplier Order Line &rarr; Product:** A supplier order line is associated with one product through SKU, and a product can appear on multiple supplier order lines

- **Warehouse &rarr; Supplier Order:** A warehouse can receive multiple supplier orders, and each supplier order is associated with one receiving warehouse through Warehouse ID
---
## Gherkin Acceptance Criteria

### US-7.1: Create Supplier Order 

#### Scenario: Create Supplier Order Form (happy path)

- **Given** the company is now ordering products from a supplier
- **And** the user is an authorized Company Admin
- **When** the Admin creates the new order information
- **And** submits the new order information
- **Then** the system validates the information
- **And** the system saves the order information
- **And** the system displays the new order information

#### Scenario: Create Supplier Order Form with Invalid Information (failure / edge)

- **Given** the company is now ordering products from a supplier
- **And** the user is an authorized Company Admin
- **When** the Admin enters invalid order information
- **And** submits the invalid order information
- **Then** the system displays an error message
- **And** the invalid information is not saved
- **And** the system requires the user to correct the invalid information before submitting again


### US-7.2: Update Order

#### Scenario: Update Order Form (happy path)

- **Given** existing order information is outdated or needs to be changed
- **And** the user is an authorized Company Admin
- **When** the Admin changes the existing order information
- **And** submits the changes
- **Then** the system validates the information
- **And** the system saves the updated order information
- **And** the system displays the updated order information

#### Scenario: Update Order with invalid information (failure / edge)

- **Given** existing order information is outdated or needs to be changed
- **And** the user is an authorized Company Admin
- **When** the Admin enters invalid order information
- **And** submits the changes
- **Then** the system displays an error message
- **And** the invalid information is not saved
- **And** the system requires the user to correct the invalid information before submitting again

### US-7.3: Deleting Order 

#### Scenario: Delete Order Form (happy path)

- **Given** a supplier order has not been submitted to the supplier and is no longer needed
- **And** the user is an authorized Company Admin
- **When** the Admin selects the order to delete
- **And** confirms the deletion
- **Then** the system deletes the order
- **And** the order is no longer displayed in the system

#### Scenario: Cannot delete Order Form If Submitted (failure / edge)

- **Given** an order form has already been submitted to the supplier
- **And** the user is an authorized Company Admin
- **When** the Admin attempts to delete the order
- **Then** the system displays an error message
- **And** the order is not deleted
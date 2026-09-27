# Feature: Product Maintenance

**Feature ID:** 4

**Branch pattern:** `feature/feature-4-maintain-product`

**Status:** Draft

**Created:** 2026-09-26

**Input:** Maintain specific information on a individual product - Add, Update, Delete - including Product ID, Product Name, SKU, UPC, Price, Supplier ID, Description

**Depends On:** feature-3-maintain-inventory

**Related:** feature-5-maintain-supplier and feature-6-maintain-customer

---

## User Stories

### US-4.1: Add Product

**As a** Company Admin

**I want to** add a new product that the company wants to sell

**So that** the company can sell the product to its customers

### US-4.2: Update Product Information

**As a** Company Admin

**I want to** update information about a product

**So that** the company can maintain accurate information about its products

### US-4.3: Delete Product

**As a** Company Admin

**I want to** delete a product that is no longer needed

**So that** the company can maintain accurate information about its products

---

## Functional Requirements (Rules)

- **FR-001:** System MUST maintain information for each specific product
- **FR-002:** System MUST allow an authorized admin to add, update, delete information about each product
- **FR-003:** Inventory MUST include Product ID, Product Name, SKU, UPC, Price, Supplier ID, Description
- **FR-004:** System MUST save information about each product
- **FR-005:** System MUST display product information
- **FR-006:** System MUST check that the information is correct and acceptable before allowing it to be saved (Validation)
- **FR-007:** System MUST only allow authorized users to maintain product information 
- **FR-008:** System MUST display error messages if required input fields are invalid or empty 
- **FR-009:** System MUST prevent a product from being deleted while a inventory record is tied to it
- **FR-010:** System MUST provide confirmation before deleting a product
---

## Key Entities

- **Inventory Record:** Represents the quantity and storage information for a specific product at a specific warehouse.

    - **Quantity**: the number / stock of pallets of a specific product
    - **Storage Location**: identifies where the product is located in the warehouse
    - **Reorder Level**: the threshold (baseline number) thats used to determined when more of a product should be ordered.



---

## Initial Data Model

| Entity | Attribute | Type | Constraints / Notes |
|---|---|---|---|
| Inventory | InventoryID | Integer | Primary Key, Unique Identifier, Required |
| Inventory | WarehouseID | Integer | Foreign Key, Required |
| Inventory | ProductID | Integer | Foreign Key, Required |
| Inventory | Quantity | Integer | Required, Number of pallets, Must be 0 or greater |
| Inventory | StorageLocation | String | Required, Identifies where the product is stored |
| Inventory | ReorderLevel | Integer | Required, Number of pallets, Must be 0 or greater |

### Associations


- **Warehouse &rarr; Inventory:** A warehouse can have multiple inventory records, and each inventory record belongs to one warehouse.

 - **Product &rarr; Inventory:** A product can have multiple inventory records, and each inventory record tracks the product's quantity, storage location, and reorder level at each warehouse.



---

## Gherkin Acceptance Criteria

### US-3.1: Add Inventory Record

#### Scenario: Add new inventory record (happy path)

- **Given** a product is available to be stored in a warehouse
- **And** the user is an authorized Company Admin
- **When** the Admin enters the new inventory record information
- **And** submits the new inventory record
- **Then** the system validates the information
- **And** the system saves the inventory record
- **And** the system displays the new inventory record information

#### Scenario: Add invalid inventory record (failure / edge)

- **Given** a product is available to be stored in a warehouse
- **And** the user is an authorized Company Admin
- **When** the Admin enters invalid inventory record information
- **And** submits the new inventory record
- **Then** the system displays an error message
- **And** the invalid information is not saved
- **And** the system requires the user to correct the invalid information before submitting again


### US-3.2: Update Inventory Record

#### Scenario: Update Inventory Record (happy path)

- **Given** inventory record information is outdated or needs to be changed
- **And** the user is an authorized Company Admin
- **When** the Admin changes the existing inventory record information
- **And** submits the changes
- **Then** the system validates the information
- **And** the system saves the updated inventory record information
- **And** the system displays the updated inventory record information

#### Scenario: Update Inventory Record with Invalid Information (failure / edge)

- **Given** inventory record information is outdated or needs to be changed
- **And** the user is an authorized Company Admin
- **When** the Admin enters invalid inventory record information
- **And** submits the changes
- **Then** the system displays an error message
- **And** the invalid information is not saved
- **And** the system requires the user to correct the invalid information before submitting again

### US-3.3: Deleting Inventory Record Information

#### Scenario: Delete Inventory Record Information (happy path)

- **Given** an inventory record has a quantity of 0 and is no longer needed
- **And** the user is an authorized Company Admin
- **When** the Admin selects the inventory record to delete
- **And** confirms the deletion
- **Then** the system deletes the inventory record
- **And** the inventory record is no longer displayed in the system

#### Scenario: Cannot delete inventory record with remaining stock (failure / edge)

- **Given** an inventory record has a quantity greater than 0
- **And** the user is an authorized Company Admin
- **When** the Admin attempts to delete the inventory record
- **Then** the system displays an error message
- **And** the inventory record is not deleted
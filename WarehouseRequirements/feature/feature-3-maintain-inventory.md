# Feature: Inventory Maintenance

**Feature ID:** 3

**Branch pattern:** `feature/feature-3-maintain-inventory`

**Status:** Draft

**Created:** 2026-09-26

**Input:** Maintain specific information on inventory - Add, Update, Delete - including Invetory ID, Warehouse ID, Product ID, Quantity, Storage Location, Reorder Level

**Depends On:** feature-2-maintain-warehouse

**Related:** feature-4-maintain-product

---

## User Stories

### US-3.1: Add Information

**As a** Company Admin

**I want to** add new inventory records

**So that** the company can maintain accurate information about the products and quantities stored in its warehouses


### US-3.2: Update Information

**As a** Company Admin

**I want to** update information about each inventory record

**So that** the company can maintain accurate information about the products and quantities stored in its warehouses

### US-3.3: Delete Information

**As a** Company Admin

**I want to** delete inventory records that are no longer needed

**So that** the company can maintain accurate information about the products and quantities stored in its warehouses


---

## Functional Requirements (Rules)

- **FR-001:** System MUST maintain information for each inventory record
- **FR-002:** System MUST allow an authorized admin to add, update, delete information about each inventory record
- **FR-003:** Inventory MUST include: Inventory ID, Warehouse ID, Product ID, Quantity, Storage Location, Reorder Level
- **FR-004:** System MUST save information about each inventory record
- **FR-005:** System MUST display inventory record information
- **FR-006:** System MUST check that the information is correct and acceptable before allowing it to be saved (Validation)
- **FR-007:** System MUST only allow authorized users to maintain inventory record information 
- **FR-008:** System MUST display error messages if required input fields are invalid or empty 
- **FR-009:** System MUST prevent an inventory record from being deleted while its quantity is greater than 0
- **FR-010:** System MUST provide confirmation before deleting an inventory record
---

## Key Entities

- **Inventory Record:** Represents the quantity and storage information for a specific product at a specific warehouse

    - **Quantity**: the number / stock of pallets of a specific product
    - **Storage Location**: identifies where the product is located in the warehouse
    - **Reorder Level**: the threshold (baseline number) thats used to determined when more of a product should be ordered



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


- **Warehouse &rarr; Inventory:** A warehouse can have multiple inventory records, and each inventory record belongs to one warehouse

 - **Product &rarr; Inventory:** A product can have multiple inventory records, and each inventory record tracks the product's quantity, storage location, and reorder level at each warehouse

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
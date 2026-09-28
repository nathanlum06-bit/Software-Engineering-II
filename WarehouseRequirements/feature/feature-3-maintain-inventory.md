# Feature: Inventory Maintenance

**Feature ID:** 3

**Branch pattern:** `feature/feature-3-maintain-inventory`

**Status:** Draft

**Created:** 2026-09-26

**Input:** Maintain specific information on inventory - Add, Update, Delete - including Inventory ID, Warehouse ID, Product ID, Bin, Slot, Case Quantity, Quantity, Minimum Inventory, Maximum Inventory, On Order, and Order Case

**Depends On:** feature-2-maintain-warehouse, feature-4-maintain-product

**Related:** 

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
- **FR-003:** Inventory MUST include: Inventory ID, Warehouse ID, Product ID, Bin, Slot, Case Quantity, Quantity, Minimum Inventory, Maximum Inventory, On Order, and Order Case
- **FR-004:** System MUST save information about each inventory record
- **FR-005:** System MUST display inventory record information
- **FR-006:** System MUST check that the information is correct and acceptable before allowing it to be saved (Validation)
- **FR-007:** System MUST only allow authorized users to maintain inventory record information 
- **FR-008:** System MUST display error messages if required input fields are invalid or empty 
- **FR-009:** System MUST prevent an inventory record from being deleted while its quantity or On Order amount is greater than 0.
- **FR-010:** System MUST provide confirmation before deleting an inventory record
---

## Key Entities

- **Inventory:** Represents the amount and location of a specific product stored at a specific warehouse. Includes Inventory ID, Warehouse ID, Product ID, Bin, Slot, Case Quantity, Quantity, Minimum Inventory, Maximum Inventory, On Order, and Order Case.

  - **Bin:** Identifies the general storage area where the product is located

  - **Slot:** Identifies the specific position within the bin where the product is stored

  - **Case Quantity:** Identifies how many individual units of the product are contained in one case

  - **Quantity:** Represents the current amount of the product in inventory

  - **Minimum Inventory:** Represents the minimum amount of the product that should normally be kept in inventory

  - **Maximum Inventory:** Represents the maximum amount of the product that should normally be kept in inventory

  - **On Order:** Represents the quantity of the product that has already been ordered from a supplier but has not yet been received
  
  - **Order Case:** Represents the number of cases normally ordered when restocking the product

- **Inventory Record:** Represents the quantity and storage information for a specific product at a specific warehouse.
---

## Initial Data Model

| Entity | Attribute | Type | Constraints / Notes |
|---|---|---|---|
| Inventory | InventoryID | Integer | Primary Key, Unique Identifier, Required |
| Inventory | WarehouseID | Integer | Foreign Key, Required, Identifies the warehouse storing the inventory |
| Inventory | ProductID | Integer | Foreign Key, Required, Identifies the product being stored |
| Inventory | Bin | String | Required, Identifies the general storage area |
| Inventory | Slot | String | Required, Identifies the specific storage position |
| Inventory | CaseQuantity | Integer | Required, Number of individual units contained in one case, Must be 0 or greater |
| Inventory | Quantity | Integer | Required, Current inventory quantity, Must be 0 or greater|
| Inventory | MinimumInventory | Integer | Required, Minimum desired inventory level, Must be 0 or greater |
| Inventory | MaximumInventory | Integer | Required, Maximum desired inventory level, Must be 0 or greater |
| Inventory | OnOrder | Integer | Required, Quantity ordered from a supplier but not yet received, Must be 0 or greater |
| Inventory | OrderCase | Integer | Required, Number of cases normally ordered when restocking, Must be 0 or greater |

### Associations

- **Warehouse &rarr; Inventory:** A warehouse can have multiple inventory records, and each inventory record belongs to one warehouse

 - **Product &rarr; Inventory:** A product can have multiple inventory records, and each inventory record tracks the product's quantity, storage location, minimum inventory, maximum inventory, and ordering information at each warehouse.

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

- **Given** an inventory record has a quantity or on order greater than 0
- **And** the user is an authorized Company Admin
- **When** the Admin attempts to delete the inventory record
- **Then** the system displays an error message
- **And** the inventory record is not deleted
# Feature: Warehouse Maintenance

**Feature ID:** 2

**Branch pattern:** `feature/feature-2-maintain-warehouse`

**Status:** Draft

**Created:** 2026-09-23

**Input:** Maintain specific information about each warehouse - Add, Update, Delete - including Warehouse ID, Company ID, Warehouse Name, Address, Phone Number, Email, Operating Hours, Aisles, Capacity, Forklift Count

**Depends On:** feature-1-maintain-company

**Related:** feature-3-maintain-inventory

---

## User Stories

### US-2.1: Add Information

**As a** Company Admin

**I want to** add new warehouses 

**So that** the company can maintain accurate information about its warehouse facilities.


### US-2.2: Update Information

**As a** Company Admin

**I want to** update information about each warehouse

**So that** the company can maintain accurate information about its warehouse facilities.

### US-2.3: Delete Information

**As a** Company Admin

**I want to** delete warehouses that are no longer in use

**So that** the company can maintain accurate information about its warehouse facilities.


---

## Functional Requirements (Rules)

- **FR-001:** System MUST maintain information for each specific warehouse
- **FR-002:** System MUST allow an authorized admin to add, update, delete information about each warehouse
- **FR-003:** Warehouse MUST include: Warehouse ID, Company ID, Warehouse Name, Address, Phone Number, Email, Operating Hours, Aisles, Capacity, Forklift Count
- **FR-004:** System MUST save information about each warehouse
- **FR-005:** System MUST display warehouse information
- **FR-006:** System MUST check that the information is correct and acceptable before allowing it to be saved (Validation)
- **FR-007:** System MUST only allow authorized users to maintain warehouse information 
- **FR-008:** System MUST display error messages if required input fields are invalid or empty 
- **FR-009:** System MUST prevent a warehouse from being deleted while it contains inventory.
- **FR-010:** System MUST provide confirmation when deleting a warehouse
---

## Key Entities

- **Company:** Represents the company that owns or operates the warehouse

- **Warehouse:** Represents a physical warehouse operated by the company. Includes Warehouse ID, Company ID, Warehouse Name, Address, Phone Number, Email, Operating Hours, Aisles, Capacity, Forklift Count

  - **Capacity:** the maximum number of pallets a warehouse can hold

- **Inventory Record:** Represents the quantity and storage information for a specific product at a specific warehouse.

---
## Initial Data Model

| Entity | Attribute | Type | Constraints / Notes |
|---|---|---|---|
| Warehouse | WarehouseID | Integer | Primary Key, Unique Identifier, Required |
| Warehouse | CompanyID | Integer | Foreign Key, Required |
| Warehouse | WarehouseName | String | Required |
| Warehouse | Address | String | Required |
| Warehouse | PhoneNumber | String | Required |
| Warehouse | Email | String | Required, Valid Email Format |
| Warehouse | OperatingHours | String | Required |
| Warehouse | Aisles | Integer | Required, Must be 0 or greater |
| Warehouse | Capacity | Integer | Required, Must be 0 or greater |
| Warehouse | ForkliftCount | Integer | Required, Must be 0 or greater |

### Associations

- **Company &rarr; Warehouse :** One company can have multiple warehouses, and each warehouse refers to the company using Company ID

- **Warehouse &rarr; Inventory:** A warehouse can have multiple inventory records, and each inventory record belongs to one warehouse.


---

## Gherkin Acceptance Criteria

### US-2.1: Add New Warehouse

#### Scenario: Add new warehouse  (happy path)

- **Given** the company acquires a new warehouse 
- **And** the user is an authorized Company Admin
- **When** the Admin enters the new warehouse information
- **And** submits the new information
- **Then** the system validates the information
- **And** the system saves the warehouse information
- **And** the system displays the new warehouse information

#### Scenario: Add invalid warehouse information (failure / edge)

- **Given** the company acquires a new warehouse 
- **And** the user is an authorized Company Admin
- **When** the Admin enters invalid warehouse information
- **And** submits the new information
- **Then** the system displays an error message
- **And** the invalid information is not saved
- **And** the system requires the user to correct the invalid information before submitting again

### US-2.2: Update Warehouse Information

#### Scenario: Update Warehouse Information (happy path)

- **Given** some warehouse information is outdated or needs to be changed 
- **And** the user is an authorized Company Admin
- **When** the Admin changes existing warehouse information
- **And** submits the changes
- **Then** the system validates the information
- **And** the system saves the warehouse information
- **And** the system displays the updated warehouse information

#### Scenario: Update Warehouse Information with Invalid Information (failure / edge)

- **Given** some warehouse information is outdated or needs to be changed 
- **And** the user is an authorized Company Admin
- **When** the Admin tries entering invalid information for existing warehouse information 
- **And** submits the changes
- **Then** the system displays an error message
- **And** the invalid information is not saved
- **And** the system requires the user to correct the invalid information before submitting again

### US-2.3: Deleting Warehouse Information

#### Scenario: Delete Warehouse Information (happy path)

- **Given** a warehouse is no longer in use 
- **And** the user is an authorized Company Admin
- **When** the Admin selects the warehouse to delete
- **And** confirms the deletion
- **Then** the system deletes the warehouse
- **And** the warehouse is no longer displayed in the system

#### Scenario: Cannot delete warehouse containing inventory (failure / edge)

- **Given** a warehouse contains inventory
- **And** the user is an authorized Company Admin
- **When** the Admin attempts to delete the warehouse
- **Then** the system displays an error message
- **And** the warehouse is not deleted 
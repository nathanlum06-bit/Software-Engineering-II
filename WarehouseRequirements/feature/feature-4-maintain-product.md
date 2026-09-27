# Feature: Product Maintenance

**Feature ID:** 4

**Branch pattern:** `feature/feature-4-maintain-product`

**Status:** Draft

**Created:** 2026-09-26

**Input:** Maintain specific information on an individual product - Add, Update, Delete - including Product ID, Product Name, SKU, UPC, Price, Supplier ID, Description

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
- **FR-003:** Product MUST include Product ID, Product Name, SKU, UPC, Price, Supplier ID, Description
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

- **Product:** Represents a specific product that the company sells. Includes Product ID, Product Name, SKU, UPC, Price, Supplier ID, and Description.
  - **SKU:** A company-assigned identifier used to track and manage a specific product internally.
  - **UPC:** A standardized product identifier associated with the product's barcode.
---

## Initial Data Model

| Entity | Attribute | Type | Constraints / Notes |
|---|---|---|---|
| Product | ProductID | Integer | Primary Key, Unique Identifier, Required |
| Product | ProductName | String | Required |
| Product | SKU | String | Required, Unique Identifier |
| Product | UPC | String | Required, Unique Barcode Identifier |
| Product | Price | Decimal | Required, Must be 0 or greater |
| Product | SupplierID | Integer | Foreign Key, Required |
| Product | Description | String | Optional |

### Associations

- **Product &rarr; Inventory:** A product can have multiple inventory records, and each inventory record tracks the product's quantity, storage location, and reorder level at each warehouse.

- **Supplier &rarr; Product**: A supplier can provide multiple products, and each product is associated with a supplier through Supplier ID

---

## Gherkin Acceptance Criteria

### US-4.1: Add Product

#### Scenario: Add new product information (happy path)

- **Given** a product is now being sold by the company
- **And** the user is an authorized Company Admin
- **When** the Admin enters the new product information
- **And** submits the new product information
- **Then** the system validates the information
- **And** the system saves the product information
- **And** the system displays the new product  information

#### Scenario: Add invalid product information (failure / edge)

- **Given** a product is now being sold by the company
- **And** the user is an authorized Company Admin
- **When** the Admin enters invalid product information
- **And** submits the invalid product information
- **Then** the system displays an error message
- **And** the invalid information is not saved
- **And** the system requires the user to correct the invalid information before submitting again


### US-4.2: Update Product

#### Scenario: Update product information (happy path)

- **Given** existing product information is outdated or needs to be changed
- **And** the user is an authorized Company Admin
- **When** the Admin changes the existing product information
- **And** submits the changes
- **Then** the system validates the information
- **And** the system saves the updated product information
- **And** the system displays the updated product information

#### Scenario: Update product with invalid information (failure / edge)

- **Given** existing product information is outdated or needs to be changed
- **And** the user is an authorized Company Admin
- **When** the Admin enters invalid product information
- **And** submits the changes
- **Then** the system displays an error message
- **And** the invalid information is not saved
- **And** the system requires the user to correct the invalid information before submitting again

### US-4.3: Deleting Product 

#### Scenario: Delete Product Information (happy path)

- **Given** a product has no inventory record and is no longer needed
- **And** the user is an authorized Company Admin
- **When** the Admin selects the product to delete
- **And** confirms the deletion
- **Then** the system deletes the product
- **And** the product is no longer displayed in the system

#### Scenario: Cannot delete product with inventory record (failure / edge)

- **Given** an product has a inventory record
- **And** the user is an authorized Company Admin
- **When** the Admin attempts to delete the product
- **Then** the system displays an error message
- **And** the product is not deleted
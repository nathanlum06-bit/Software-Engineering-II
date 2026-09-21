# Feature: Product & Inventory Management

**Feature ID:** 2  
**Branch pattern:** `feature-2-product-inventory-management`  
**Status:** Ready  
**Created:** 11/20/26  
**Input:** Maintain product information and provide accurate inventory quantities and warehouse locations.

**Depends on:** User authorization so that only authorized warehouse employees can access inventory information and functions.

## User Stories:

### US-2.1 View Product Information

**As a** warehouse employee  
**I want** to view product information  
**So that** I can identify products and access the information needed to manage inventory

### US-2.2 Track Inventory

**As a** warehouse employee  
**I want** the application to track the quantity of each product in inventory  
**So that** I can access accurate inventory information and reduce inventory errors

### US-2.3 Track Product Locations

**As a** warehouse employee  
**I want** to view where products are stored in the warehouse  
**So that** I can locate products when receiving, restocking, or picking inventory

## Requirements

- **FR-2.1:** The application MUST maintain a record for each product in the warehouse.

- **FR-2.2:** The application MUST store the internal product number for each product.

- **FR-2.3:** The application MUST store the supplier product number for each product.

- **FR-2.4:** The application MUST store the individual item UPC and case-level UPC when applicable.

- **FR-2.5:** The application MUST store a description for each product.

- **FR-2.6:** The application MUST track the quantity of each product currently on hand.

- **FR-2.7:** The application MUST track the location where each product is stored in the warehouse.

- **FR-2.8:** The application MUST maintain the case size for each product.

- **FR-2.9:** The application MUST maintain the selling price and supplier cost for each product.

- **FR-2.10:** The application MUST maintain the minimum and maximum inventory levels for each product.

- **FR-2.11:** The application MUST track the quantity of each product currently on order.

- **FR-2.12:** The application MUST allow authorized warehouse employees to access current product and inventory information.

---

## Key Entities

- **Product** — Represents an item that the warehouse purchases, stores, and sells to customers.

- **Inventory** — Represents the quantity of a product currently held by the warehouse.

- **Warehouse Location** — Represents where inventory is stored within the warehouse.

## Initial Data Model

### Product

| Attribute | Description |
|---|---|
| productId | Unique identifier for the product |
| internalProductNumber | Internal product number used by the company |
| supplierProductNumber | Product number used by the supplier |
| individualUPC | UPC identifying an individual product |
| caseUPC | UPC identifying a case of the product |
| description | Description of the product |
| caseSize | Number of individual items contained in a case |
| sellingPrice | Price charged to customers |
| supplierCost | Cost paid to the supplier |
| minimumLevel | Minimum inventory level |
| maximumLevel | Maximum inventory level |

### Inventory

| Attribute | Description |
|---|---|
| inventoryId | Unique identifier for the inventory record |
| productId | Identifies the product |
| quantityOnHand | Current quantity physically in the warehouse |
| quantityOnOrder | Quantity ordered from suppliers but not yet received |
| locationId | Identifies where the inventory is stored |

### Warehouse Location

| Attribute | Description |
|---|---|
| locationId | Unique identifier for the warehouse location |
| aisle | Warehouse aisle identifier |
| row | Row within the aisle |
| column | Column within the row |
| slot | Storage slot identifier |

---

## Acceptance Criteria (Gherkin)

### US-2.1 View Product Information

#### Scenario: User views product information

- **Given** the product exists in the warehouse system
- **When** an authorized user searches for the product
- **Then** the application displays the product information
- **And** the application displays the product's inventory information

#### Scenario: Product does not exist

- **Given** a product does not exist in the warehouse system
- **When** an authorized user searches for the product
- **Then** the application indicates that the product could not be found

### US-2.2 Track Inventory

#### Scenario: User views current inventory

- **Given** a product exists in the warehouse system
- **When** an authorized user views the product's inventory
- **Then** the application displays the current quantity on hand
- **And** the application displays the quantity currently on order

#### Scenario: Inventory quantity changes

- **Given** a product has inventory recorded in the warehouse
- **When** the inventory quantity changes
- **Then** the application updates the product's inventory quantity

### US-2.3 Track Product Locations

#### Scenario: User views product location

- **Given** a product is stored in the warehouse
- **When** an authorized user views the product's inventory information
- **Then** the application displays the warehouse location of the product

#### Scenario: Product is stored in an alternative location

- **Given** the designated location for a product is unavailable
- **When** the product is placed in an alternative warehouse location
- **Then** the application records the alternative location for the product
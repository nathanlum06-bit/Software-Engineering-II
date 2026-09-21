# Feature: Receiving & Put-Away

**Feature ID:** 5  
**Branch pattern:** `feature-5-receiving-put-away`  
**Status:** Ready  
**Created:** 11/20/26  
**Input:** Receive incoming products from suppliers and record where products are stored in the warehouse.

**Depends on:** Product & Inventory Management and User Authorization so that received products can be identified, inventory can be updated, and only authorized employees can perform receiving and put-away functions.

## User Stories:

### US-5.1 Receive Products

**As a** warehouse employee  
**I want** to record products received from suppliers  
**So that** incoming inventory is accurately recorded in the warehouse system

### US-5.2 Assign Storage Location

**As a** warehouse employee  
**I want** to assign a storage location to received products  
**So that** products can be located after they are placed in the warehouse

### US-5.3 Record Put-Away

**As a** warehouse employee  
**I want** to record where received products are placed  
**So that** inventory quantities and locations remain accurate

### US-5.4 Handle Alternative Storage Locations

**As a** warehouse employee  
**I want** to record an alternative storage location when the designated location is unavailable  
**So that** the system reflects where the product is actually stored

## Requirements

- **FR-5.1:** The application MUST allow authorized warehouse employees to record products received from suppliers.

- **FR-5.2:** The application MUST record the quantity of each product received.

- **FR-5.3:** The application MUST associate received products with the appropriate product record.

- **FR-5.4:** The application MUST allow authorized warehouse employees to assign a warehouse location to received products.

- **FR-5.5:** The application MUST record the warehouse location where received products are placed.

- **FR-5.6:** The application MUST allow employees to record an alternative storage location when the designated location is unavailable.

- **FR-5.7:** The application MUST update inventory quantities when products are received and placed into storage.

- **FR-5.8:** The application MUST maintain the recorded location of stored products.

- **FR-5.9:** The application MUST provide current product location information after put-away is completed.

---

## Key Entities

- **Receiving Record** — Represents products received from a supplier.

- **Put-Away Record** — Represents the placement of received products into a warehouse location.

- **Product** — Represents the product being received and stored.

- **Warehouse Location** — Represents the physical location where products are stored.

## Initial Data Model

### Receiving Record

| Attribute | Description |
|---|---|
| receivingId | Unique identifier for the receiving record |
| productId | Identifies the product received |
| quantityReceived | Quantity of the product received |
| receivedDate | Date the product was received |
| supplierId | Identifies the supplier providing the product |

### Put-Away Record

| Attribute | Description |
|---|---|
| putAwayId | Unique identifier for the put-away record |
| receivingId | Identifies the receiving record |
| productId | Identifies the product being stored |
| quantityPlaced | Quantity placed into storage |
| locationId | Identifies the warehouse location used |

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

### US-5.1 Receive Products

#### Scenario: Employee records received products

- **Given** a product is being received from a supplier
- **When** an authorized employee records the received product and quantity
- **Then** the application creates a receiving record
- **And** the received quantity is recorded

#### Scenario: Employee receives an unknown product

- **Given** the received product does not exist in the warehouse system
- **When** an employee attempts to record the received product
- **Then** the application does not record the product
- **And** the application indicates that the product could not be found

### US-5.2 Assign Storage Location

#### Scenario: Employee assigns a storage location

- **Given** a product has been received
- **When** an authorized employee assigns a warehouse location
- **Then** the application records the assigned location for the product

### US-5.3 Record Put-Away

#### Scenario: Employee completes put-away

- **Given** a product has been received
- **And** a warehouse location has been selected
- **When** the employee records the product as placed in the location
- **Then** the application records the put-away
- **And** the application updates the product's inventory quantity
- **And** the application records the product's warehouse location

### US-5.4 Handle Alternative Storage Locations

#### Scenario: Designated location is unavailable

- **Given** the designated storage location for a product is unavailable
- **When** the employee places the product in an alternative location
- **Then** the application records the alternative location
- **And** the application associates the product with the alternative location
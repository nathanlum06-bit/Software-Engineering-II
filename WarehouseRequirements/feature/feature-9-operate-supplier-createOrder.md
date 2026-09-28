# Feature: Create Order Form to Supplier

**Feature ID:** 9

**Branch pattern:** `feature/feature-9-operate-supplier-createOrder`

**Status:** Draft

**Created:** 2026-09-23

**Input:** Create, Calculate, Review, Submit a supplier order form for products needed by the warehouse. This includes Quantity, Minimum Inventory, Maximum Inventory, On Order, and Order Case from the Inventory record. The system calculates Quantity on Hand using Quantity + On Order.

**Depends On:**feature-3-maintain-inventory, feature-7-maintain-supplier-orderForm

**Related:** feature-10-operate-supplier-receiveOrder

---

## User Stories

### US-9.1: Create Supplier Order

**As a** Company Admin

**I want to** create an order form for a supplier

**So that** the company can restock products in the warehouse

### US-9.2: Calculate Order

**As a** Company Admin

**I want to** calculate the total quantity of cases needed and the total cost

**So that** the company knows how many cases to order and the total cost of the supplier order

### US-9.3: Review Supplier Order

**As a** Company Admin

**I want to** review the supplier order before submitting it

**So that** I can verify the order information is correct before sending it to the supplier

### US-9.4: Submit Supplier Order

**As a** Company Admin

**I want to** send the order form to the supplier

**So that** the supplier can process the company's order


## Functional Requirements (Rules)

- **FR-001:** System MUST create a supplier order form for the warehouse company
- **FR-002:** System MUST allow an authorized Company Admin to fill out the supplier order form
- **FR-003:** Supplier Order MUST include Company ID, Supplier ID, Customer Number, Order Date, P.O. Number, Authorized By, Order Total, and Warehouse ID
- **FR-004:** Each Supplier Order Line MUST include SKU, Description, Cases, Price, and Product Total
- **FR-005:** System MUST retrieve the current inventory record for the selected products, including Quantity, Minimum Inventory, Maximum Inventory, On Order, and Order Case
- **FR-006:** System MUST calculate Quantity on Hand using data from the Inventory record. Quantity on Hand = Quantity + On Order
- **FR-007:** System MUST determine the quantity needed to reorder by subtracting Quantity on Hand from Maximum Inventory when Quantity on Hand is less than Minimum Inventory
- **FR-008:** System MUST calculate the number of Cases needed by dividing the quantity needed to reorder by Order Case
- **FR-009:** System MUST round the number of Cases up to the next whole case when the quantity needed cannot be fulfilled by a whole number of cases
- **FR-010:** System MUST calculate the Product Total by multiplying the number of Cases by the Price per Case
- **FR-011:** System MUST calculate the Order Total by adding the Product Total for each product in the order
- **FR-012:** System MUST allow the authorized Company Admin to review the supplier order before submitting it
- **FR-013:** System MUST check that the supplier order information is correct and acceptable before allowing it to be submitted (Validation)
- **FR-014:** System MUST only allow authorized users to create and submit supplier orders
- **FR-015:** System MUST display error messages if required supplier order information is invalid or empty
- **FR-016:** System MUST save the supplier order after it has been successfully submitted
---

## Key Entities

- **Calculations:** Data is retrieved from Feature 3 Inventory and Feature 7 Supplier Order Form.
    - **From Feature 3 — Inventory:** Quantity, Minimum Inventory, Maximum Inventory, On Order, and Order Case
    - **From Feature 7 — Supplier Order Form:** Supplier Order information including Supplier ID, Company ID, Warehouse ID, SKU, Price, Cases, Product Total, and Order Total
- **Quantity on Hand:** Calculated using Quantity and On Order from the Inventory record.
    - **Quantity on Hand = Quantity + On Order**
- **Quantity Needed:** The amount of product needed to bring Quantity on Hand up to Maximum Inventory.
    - **Quantity Needed = Maximum Inventory - Quantity on Hand**
- **Cases Needed:** The number of cases required to fulfill the Quantity Needed.
    - **Cases Needed = Quantity Needed / Order Case (ROUND UP)**
- **Product Total:** The cost of each Supplier Order Line.
    - **Product Total = Cases * Price per Case**
- **Order Total:** The total cost of the Supplier Order.
    - **Order Total = Sum of all Product Totals**
- **Restock Logic:** If Quantity on Hand is less than Minimum Inventory, the system MUST determine the Quantity Needed to reach Maximum Inventory.
---

## Initial Data Model

### Data From Feature 3 — Inventory

| Entity | Attribute | Type | Constraints / Notes |
|---|---|---|---|
| Inventory | InventoryID | Integer | Primary Key, Unique Identifier, Required |
| Inventory | WarehouseID | Integer | Foreign Key → Warehouse, Required |
| Inventory | ProductID | Integer | Foreign Key → Product, Required |
| Inventory | Quantity | Integer | Required, Must be greater than or equal to 0 |
| Inventory | MinimumInventory | Integer | Required, Must be greater than or equal to 0 |
| Inventory | MaximumInventory | Integer | Required, Must be greater than or equal to MinimumInventory |
| Inventory | OnOrder | Integer | Required, Must be greater than or equal to 0 |
| Inventory | OrderCase | Integer | Required, Must be greater than 0 |

### Data From Feature 7 — Supplier Order Form

| Entity | Attribute | Type | Constraints / Notes |
|---|---|---|---|
| SupplierOrder | OrderID | Integer | Primary Key, Unique Identifier, Required |
| SupplierOrder | CompanyID | Integer | Foreign Key → Company, Required |
| SupplierOrder | SupplierID | Integer | Foreign Key → Supplier, Required |
| SupplierOrder | WarehouseID | Integer | Foreign Key → Warehouse, Required |
| SupplierOrder | CustomerNumber | String | Required |
| SupplierOrder | OrderDate | Date | Required |
| SupplierOrder | PONumber | String | Unique Purchase Order Number, Required |
| SupplierOrder | AuthorizedBy | Integer | Required |
| SupplierOrder | OrderTotal | Decimal | Calculated from Supplier Order Lines, Required |
| SupplierOrderLine | OrderID | Integer | Foreign Key → SupplierOrder, Required |
| SupplierOrderLine | SKU | String | Foreign Key → Product, Required |
| SupplierOrderLine | Description | String | Required |
| SupplierOrderLine | Cases | Integer | Required, Must be greater than 0 |
| SupplierOrderLine | Price | Decimal | Price per case, Required, Must be greater than or equal to 0 |
| SupplierOrderLine | ProductTotal | Decimal | Calculated as Cases × Price, Required |

### Associations

- **Inventory Record &rarr; Supplier Order Form:** The Inventory Record provides the Quantity, Minimum Inventory, Maximum Inventory, On Order, and Order Case used to calculate the quantity of product and number of cases needed for the Supplier Order Form.

- **Supplier Order Form &rarr; Supplier Order:** The Supplier Order Form uses the calculated quantities and product information to create a Supplier Order.

- **Warehouse &rarr; Inventory Record:** One warehouse can have many Inventory Records, and each Inventory Record belongs to one Warehouse.

- **Company &rarr; Warehouse:** One company can have multiple warehouses, and each warehouse refers to the company using Company ID.

- **Supplier &rarr; Supplier Order:** One supplier can have many Supplier Orders, and each Supplier Order is associated with one Supplier.
---

## Gherkin Acceptance Criteria

### US-9.1: Create Order

#### Scenario: Create Supplier Order (happy path)

- **Given** no supplier order form has been initialized in the system
- **And** the user is an authorized Company Admin
- **When** the Admin creates a supplier order
- **And** the Admin enters the required supplier order information
- **And** the system validates the information
- **Then** the system calculates additional information


#### Scenario: Create Supplier Order with Invalid Information (failure / edge)

- **Given** no supplier order form has been initialized in the system
- **And** the user is an authorized Company Admin
- **When** the Admin creates a supplier order
- **And** the Admin enters invalid supplier order information
- **Then** the system displays an error message
- **And** the system does not create the supplier order
- **And** the system requires the Admin to correct the invalid information before continuing

### US-9.2: Calculate Order

#### Scenario: Calculate Supplier Order (happy path)

- **Given** the supplier order form's fields are filled and valid
- **And** the user is an authorized Company Admin
- **When** the Admin requests the supplier order to be calculated
- **Then** the system calculates Quantity on Hand
- **And** the system calculates Quantity Needed
- **And** the system calculates Cases Needed
- **And** the system calculates Product Total
- **And** the system calculates Order Total
- **And** the system displays the calculated supplier order information

#### Scenario: Calculate Supplier Order with Invalid Information (failure / edge)

- **Given** the supplier order form contains empty or invalid required information
- **And** the user is an authorized Company Admin
- **When** the Admin requests the supplier order to be calculated
- **Then** the system displays an error message
- **And** the system does not calculate the supplier order
- **And** the system requires the Admin to correct the invalid information before calculating again

### US-9.3: Review Order

#### Scenario: Review Supplier Order (happy path)

- **Given** calculations have been performed
- **And** the user is an authorized Company Admin
- **When** the Company Admin requests to review the supplier order
- **Then** the system displays a review containing a summary of the supplier order
- **And** the review displays the supplier information
- **And** the review displays the products and quantities being ordered
- **And** the review displays the Product Total and Order Total

#### Scenario: Review Supplier Order with Incomplete Calculations (failure / edge)

- **Given** the supplier order has not completed all required calculations
- **And** the user is an authorized Company Admin
- **When** the Company Admin attempts to review the supplier order
- **Then** the system displays an error message
- **And** the system prevents the supplier order from being submitted
- **And** the system requires the calculations to be completed before reviewing the order

### US-9.4: Submit Order

#### Scenario: Submit Supplier Order (happy path)

- **Given** the supplier order has been calculated
- **And** the supplier order has been reviewed
- **And** the user is an authorized Company Admin
- **When** the Company Admin submits the supplier order
- **Then** the system validates the supplier order
- **And** the system saves the supplier order
- **And** the system displays a confirmation message

#### Scenario: Submit Supplier Order with Invalid Information (failure / edge)

- **Given** the supplier order has been reviewed
- **And** the user is an authorized Company Admin
- **When** the Company Admin submits the supplier order
- **And** the supplier order contains invalid or incomplete information
- **Then** the system displays an error message
- **And** the system does not submit the supplier order
- **And** the system requires the Admin to correct the information before submitting again
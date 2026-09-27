# Feature: Customer Order Form Maintenance

**Feature ID:** 8

**Branch pattern:** `feature/feature-8-maintain-customer-orderForm`

**Status:** Draft

**Created:** 2026-09-23

**Input:** Maintain customer order information - Add, Update, Delete - including Ordered By (Customer ID), Ordered From (Company ID), Customer Number, Order Date, P.O Number, SKU, Description, Cases, Price, Product Total, Order Total, Authorized By, Warehouse ID

**Depends On:** feature-1-maintain-company, feature-2-maintain-warehouse, feature-4-maintain-product, feature-6-maintain-customer

**Related:** feature-7-maintain-supplier-orderForm

---

## User Stories

### US-8.1: Add Customer Order

**As a** Company Admin

**I want to** add an order form for a customer

**So that** the company can take orders from customers

### US-8.2: Update Customer Order

**As a** Company Admin

**I want to** update information about a customer's order

**So that** the order's information remains accurate and up to date

### US-8.3: Delete Customer Order

**As a** Company Admin

**I want to** delete an order that the customer no longer needs

**So that** the order's information remains accurate and up to date

---

## Functional Requirements (Rules)

- **FR-001:** System MUST maintain information for each order for a customer
- **FR-002:** System MUST allow an authorized admin to add, update, delete information about each order
- **FR-003:** Customer Order MUST include Company ID, Customer ID, Customer Number, Order Date, P.O. Number, Authorized By, Order Total, Warehouse ID
- **FR-004:** Each Customer Order Line MUST include SKU, Description, Cases, Price, and Product Total.
- **FR-005:** System MUST save information about the order
- **FR-006:** System MUST display order information
- **FR-007:** System MUST check that the information is correct and acceptable before allowing it to be saved (Validation)
- **FR-008:** System MUST only allow authorized users to maintain order information 
- **FR-009:** System MUST display error messages if required input fields are invalid or empty 
- **FR-010:** System MUST prevent a customer order from being deleted after it has been accepted by the company
- **FR-011:** System MUST provide confirmation before deleting an order
---

## Key Entities

- **Customer Order:** Represents the entire order placed by a customer with our warehouse company. Contains information that applies to the overall order, such as Order ID, customer, order date, P.O. number, authorized person, warehouse fulfilling the order, and order status.

- **Customer Order Line:** one product within a customer order. Contains the product's SKU, description, cases, price, and product total. An order can contain multiple customer order lines

- **Customer:** Represents a company or business that buys products from our warehouse company.

- **Product:** Represents a specific product that the company sells. Includes Product ID, Product Name, SKU, Product UPC, Case UPC, Price, Supplier ID, and Description
  - **SKU:** A company-assigned identifier used to track and manage a specific product internally

- **Bill of Lading:** A document that records the shipment of products being transported from a supplier to the warehouse or from the warehouse to a customer. It includes shipment details and identifies the products and quantities being transported
---

## Initial Data Model

| Entity | Attribute | Type | Constraints / Notes |
|---|---|---|---|
| CustomerOrder | OrderID | Integer | Primary Key, Unique Identifier, Required |
| CustomerOrder | CompanyID | Integer | Foreign Key, Required, Identifies the company receiving the order |
| CustomerOrder | CustomerID | Integer | Foreign Key, Required, Identifies the customer placing the order |
| CustomerOrder | CustomerNumber | Integer | Required, Identifies the customer's customer/account number with the company |
| CustomerOrder | OrderDate | Date | Required |
| CustomerOrder | PONumber | String | Required, Unique Identifier |
| CustomerOrder | AuthorizedBy | String | Required, Identifies the person who authorized the order |
| CustomerOrder | OrderTotal | Decimal | Required, Calculated from the total of all ProductTotal values |
| CustomerOrder | WarehouseID | Integer | Foreign Key, Required, Identifies the warehouse fulfilling the order |
| CustomerOrder | BOL | String | Required, References Bill of Lading |
| CustomerOrderLine | OrderID | Integer | Foreign Key, Required, References CustomerOrder |
| CustomerOrderLine | SKU | String | Foreign Key, Required, References Product SKU |
| CustomerOrderLine | Description | String | Required, Describes the product |
| CustomerOrderLine | Cases | Integer | Required, Must be greater than 0 |
| CustomerOrderLine | Price | Decimal | Required, Must be greater than or equal to 0, Price for the order line |
| CustomerOrderLine | ProductTotal | Decimal | Required, Calculated from Cases × Price |



### Associations

- **Customer &rarr; Customer Order:** A customer can have multiple customer orders, and each customer order is associated with one customer through customer ID

- **Customer Order &rarr; Customer Order Line:** A customer order can contain multiple customer order lines, and each customer order line is associated with one customer order through Order ID

- **Customer Order Line &rarr; Product:** A customer order line is associated with one product through SKU, and a product can appear on multiple customer order lines

- **Warehouse &rarr; Customer Order:** A warehouse can fulfill multiple customer orders, and each customer order is associated with one fulfilling warehouse through Warehouse ID

---
## Gherkin Acceptance Criteria

### US-8.1: Add Customer Order

#### Scenario: Add Customer Order Form (happy path)

- **Given** a customer wants to order products from our warehouse company
- **And** the user is an authorized Company Admin
- **When** the Admin adds the new order information
- **And** submits the new order information
- **Then** the system validates the information
- **And** the system saves the order information
- **And** the system displays the new order information

#### Scenario: Add Customer Order Form with Invalid Information (failure / edge)

- **Given** a customer wants to order products from our warehouse company
- **And** the user is an authorized Company Admin
- **When** the Admin enters invalid order information
- **And** submits the invalid order information
- **Then** the system displays an error message
- **And** the invalid information is not saved
- **And** the system requires the user to correct the invalid information before submitting again


### US-8.2: Update Order

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

### US-8.3: Deleting Order 

#### Scenario: Delete Order Form (happy path)

- **Given** a customer order has not been accepted by the company 
- **And** the user is an authorized Company Admin
- **When** the Admin selects the order to delete
- **And** confirms the deletion
- **Then** the system deletes the order
- **And** the order is no longer displayed in the system

#### Scenario: Cannot delete Order Form If Accepted (failure / edge)

- **Given** a customer order has been accepted already by the company 
- **And** the user is an authorized Company Admin
- **When** the Admin attempts to delete the order
- **Then** the system displays an error message
- **And** the order is not deleted
# Feature: Customer Order Management

**Feature ID:** 4  
**Branch pattern:** `feature-4-customer-order-management`  
**Status:** Ready  
**Created:** 11/20/26  
**Input:** Manage customer orders from order placement through preparation for fulfillment and delivery.

**Depends on:** Customer Management and Product & Inventory Management so that orders can be associated with customers and contain products available in the warehouse.

## User Stories:

### US-4.1 Create Customer Order

**As a** warehouse employee  
**I want** to create a customer order  
**So that** the customer's requested products can be recorded for fulfillment

### US-4.2 View Customer Order

**As a** warehouse employee  
**I want** to view a customer order  
**So that** I can see the products and quantities requested by the customer

### US-4.3 Schedule Customer Order

**As a** warehouse employee  
**I want** to record the scheduled delivery date for a customer order  
**So that** the order can be prepared for the appropriate delivery route

### US-4.4 Track Order Status

**As a** warehouse employee  
**I want** to track the status of a customer order  
**So that** I can determine whether the order has been placed, fulfilled, shipped, or delivered

## Requirements

- **FR-4.1:** The application MUST maintain a record for each customer order.

- **FR-4.2:** The application MUST associate each customer order with a customer.

- **FR-4.3:** The application MUST record the products requested in each customer order.

- **FR-4.4:** The application MUST record the quantity of each product requested in a customer order.

- **FR-4.5:** The application MUST record the date the customer order was placed.

- **FR-4.6:** The application MUST record the scheduled delivery date for each customer order.

- **FR-4.7:** The application MUST require customer orders to be placed at least two business days before the scheduled delivery date.

- **FR-4.8:** The application MUST allow authorized warehouse employees to view customer orders.

- **FR-4.9:** The application MUST track the status of each customer order.

- **FR-4.10:** The application MUST allow authorized warehouse employees to update the status of a customer order.

- **FR-4.11:** The application MUST provide order information needed for warehouse fulfillment and delivery.

---

## Key Entities

- **Customer Order** — Represents an order placed by a customer for products from the warehouse.

- **Order Line** — Represents a product and requested quantity within a customer order.

- **Customer** — Represents the convenience store that placed the order.

- **Product** — Represents a product requested by the customer.

## Initial Data Model

### Customer Order

| Attribute | Description |
|---|---|
| orderId | Unique identifier for the customer order |
| customerId | Identifies the customer who placed the order |
| orderDate | Date the order was placed |
| scheduledDeliveryDate | Scheduled date for delivery |
| status | Current status of the order |

### Order Line

| Attribute | Description |
|---|---|
| orderLineId | Unique identifier for the order line |
| orderId | Identifies the customer order |
| productId | Identifies the product requested |
| quantity | Quantity of the product requested |

---

## Acceptance Criteria (Gherkin)

### US-4.1 Create Customer Order

#### Scenario: User creates a customer order

- **Given** the customer exists in the warehouse system
- **When** an authorized user creates an order for the customer
- **Then** the application creates a customer order
- **And** the order is associated with the customer
- **And** the requested products and quantities are recorded

#### Scenario: User creates an order for an invalid customer

- **Given** the customer does not exist in the warehouse system
- **When** an authorized user attempts to create an order for the customer
- **Then** the application does not create the order
- **And** the application indicates that the customer could not be found

### US-4.2 View Customer Order

#### Scenario: User views a customer order

- **Given** the customer order exists
- **When** an authorized user views the order
- **Then** the application displays the customer associated with the order
- **And** the application displays the products and quantities requested

#### Scenario: User views an order that does not exist

- **Given** the customer order does not exist
- **When** an authorized user searches for the order
- **Then** the application indicates that the order could not be found

### US-4.3 Schedule Customer Order

#### Scenario: Order is scheduled within the allowed time

- **Given** a customer order has been created
- **When** the user schedules the order for delivery at least two business days after the order date
- **Then** the application records the scheduled delivery date

#### Scenario: Order is scheduled too soon

- **Given** a customer order has been created
- **When** the user attempts to schedule the order for delivery less than two business days after the order date
- **Then** the application rejects the scheduled delivery date
- **And** the application indicates that the order must be placed at least two business days before delivery

### US-4.4 Track Order Status

#### Scenario: User updates order status

- **Given** a customer order exists
- **When** an authorized user updates the order status
- **Then** the application saves the new order status

#### Scenario: User views order status

- **Given** a customer order exists
- **When** an authorized user views the order
- **Then** the application displays the current status of the order
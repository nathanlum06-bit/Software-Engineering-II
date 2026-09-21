# Feature: Customer Management

**Feature ID:** 3  
**Branch pattern:** `feature-3-customer-management`  
**Status:** Ready  
**Created:** 11/20/26  
**Input:** Maintain customer information for the convenience stores served by the wholesaler.

**Depends on:** User Authorization so that only authorized warehouse employees can access customer information and functions.

## User Stories:

### US-3.1 View Customer Information

**As a** warehouse employee  
**I want** to view customer information  
**So that** I can identify customers and access the information needed to manage their orders

### US-3.2 Maintain Customer Information

**As a** warehouse employee  
**I want** to maintain customer information  
**So that** the warehouse has current customer information for order processing and delivery

### US-3.3 Access Customer Information for Orders

**As a** warehouse employee  
**I want** to access customer information when processing orders  
**So that** customer orders can be associated with the correct customer

## Requirements

- **FR-3.1:** The application MUST maintain a record for each customer.

- **FR-3.2:** The application MUST maintain identifying information for each customer.

- **FR-3.3:** The application MUST maintain contact information for each customer.

- **FR-3.4:** The application MUST allow authorized warehouse employees to view customer information.

- **FR-3.5:** The application MUST allow authorized warehouse employees to update customer information.

- **FR-3.6:** The application MUST associate customer orders with the appropriate customer.

- **FR-3.7:** The application MUST provide customer information needed for order fulfillment and delivery.

---

## Key Entities

- **Customer** — Represents a convenience store that purchases products from the wholesaler.

- **Customer Order** — Represents an order placed by a customer for products from the warehouse.

## Initial Data Model

### Customer

| Attribute | Description |
|---|---|
| customerId | Unique identifier for the customer |
| customerName | Name of the customer or convenience store |
| contactInformation | Contact information for the customer |
| address | Customer delivery address |

### Customer Order

| Attribute | Description |
|---|---|
| orderId | Unique identifier for the customer order |
| customerId | Identifies the customer who placed the order |
| orderDate | Date the order was placed |
| scheduledDeliveryDate | Scheduled date for delivery |

---

## Acceptance Criteria (Gherkin)

### US-3.1 View Customer Information

#### Scenario: User views customer information

- **Given** the customer exists in the warehouse system
- **When** an authorized user searches for the customer
- **Then** the application displays the customer's information

#### Scenario: Customer does not exist

- **Given** a customer does not exist in the warehouse system
- **When** an authorized user searches for the customer
- **Then** the application indicates that the customer could not be found

### US-3.2 Maintain Customer Information

#### Scenario: User updates customer information

- **Given** the customer exists in the warehouse system
- **When** an authorized user updates the customer's information
- **Then** the application saves the updated customer information

#### Scenario: User attempts to update customer information without authorization

- **Given** the user is not authorized to modify customer information
- **When** the user attempts to update the customer's information
- **Then** the application denies the update

### US-3.3 Access Customer Information for Orders

#### Scenario: User accesses customer information for an order

- **Given** a customer order exists
- **When** an authorized user views the order
- **Then** the application identifies the customer associated with the order
- **And** the application provides the customer's relevant information

#### Scenario: Customer order has no valid customer

- **Given** a customer order does not have a valid associated customer
- **When** an authorized user views the order
- **Then** the application indicates that the customer information could not be found
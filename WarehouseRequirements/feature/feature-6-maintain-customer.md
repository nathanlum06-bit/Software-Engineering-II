# Feature: Customer Maintenance

**Feature ID:** 6

**Branch pattern:** `feature/feature-6-maintain-customer`

**Status:** Draft

**Created:** 2026-09-26

**Input:** Maintain customer information - Add, Update, Delete - including Customer ID, Store Name, Address, Phone Number, Email, Contact Name, Business Hours, Route 

**Depends On:** 

**Related:** feature-8-maintain-customer-orderForm
---

## User Stories

### US-6.1: Create Customer Information

**As a** Company Admin

**I want to** add a customer's information

**So that** the company knows who to receive orders from

### US-6.2: Update Customer Information

**As a** Company Admin

**I want to** update information about our customers

**So that** the customer's information remains accurate and up to date

### US-6.3: Delete Customer Information

**As a** Company Admin

**I want to** delete customers that the company no longer supplies

**So that** the customer's information remains accurate and up to date

---

## Functional Requirements (Rules)

- **FR-001:** System MUST maintain information for each customer
- **FR-002:** System MUST allow an authorized admin to add, update, delete information about each customer
- **FR-003:** Customer MUST include: Customer ID, Store Name, Address, Phone Number, Email, Contact Name, Business Hours, Route 
- **FR-004:** System MUST save information about the customer
- **FR-005:** System MUST display customer information
- **FR-006:** System MUST check that the information is correct and acceptable before allowing it to be saved (Validation)
- **FR-007:** System MUST only allow authorized users to maintain customer information 
- **FR-008:** System MUST display error messages if required input fields are invalid or empty 
- **FR-009:** System MUST prevent a customer from being deleted while orders are associated with it
- **FR-010:** System MUST provide confirmation before deleting a customer
---

## Key Entities

- **Customer:** Represents a business or convenience store that orders products from our warehouse company. Includes Customer ID, Store Name, Address, Phone Number, Email, Contact Name, Business Hours, and Route

   - **Route:** Represents the delivery route assigned to a customer. (For complexity sake, the route is stored directly as part of the Customer information rather than being maintained as a separate entity.) 

      - **Route Includes:** Route ID, Route Name/Number, Delivery Schedule, Delivery Day, Start Time, End Time, Assigned Driver, Assigned Truck, Number of Stops

- **Product:** Represents a product supplied by a supplier and sold by our company to customers
---

## Initial Data Model

| Entity | Attribute | Type | Constraints / Notes |
|---|---|---|---|
| Customer | CustomerID | Integer | Primary Key, Unique Identifier, Required |
| Customer | StoreName | String | Required |
| Customer | Address | String | Required |
| Customer | PhoneNumber | String | Required |
| Customer | Email | String | Required, Valid Email Format |
| Customer | ContactName | String | Required |
| Customer | BusinessHours | String | Required |
| Customer | Route | Object | Required, Identifies the customer's delivery route |


### Associations

WORK ON

---
## Gherkin Acceptance Criteria

### US-6.1: Add Customer

#### Scenario: Add new customer information (happy path)

- **Given** a customer is now ordering products from our company
- **And** the user is an authorized Company Admin
- **When** the Admin enters the new customer information
- **And** submits the new customer information
- **Then** the system validates the information
- **And** the system saves the customer information
- **And** the system displays the new customer  information

#### Scenario: Add invalid customer information (failure / edge)

- **Given** a customer is now ordering products from our company
- **And** the user is an authorized Company Admin
- **When** the Admin enters invalid customer information
- **And** submits the invalid customer information
- **Then** the system displays an error message
- **And** the invalid information is not saved
- **And** the system requires the user to correct the invalid information before submitting again


### US-6.2: Update Customer

#### Scenario: Update customer information (happy path)

- **Given** existing customer information is outdated or needs to be changed
- **And** the user is an authorized Company Admin
- **When** the Admin changes the existing customer information
- **And** submits the changes
- **Then** the system validates the information
- **And** the system saves the updated customer information
- **And** the system displays the updated customer information

#### Scenario: Update customer with invalid information (failure / edge)

- **Given** existing customer information is outdated or needs to be changed
- **And** the user is an authorized Company Admin
- **When** the Admin enters invalid customer information
- **And** submits the changes
- **Then** the system displays an error message
- **And** the invalid information is not saved
- **And** the system requires the user to correct the invalid information before submitting again

### US-6.3: Deleting Customer 

#### Scenario: Delete customer information (happy path)

- **Given** a customer no longer orders products from our company
- **And** the user is an authorized Company Admin
- **When** the Admin selects the customer to delete
- **And** confirms the deletion
- **Then** the system deletes the customer
- **And** the customer is no longer displayed in the system

#### Scenario: Cannot delete customer with orders associated (failure / edge)

- **Given** a customer has a orders associated with it
- **And** the user is an authorized Company Admin
- **When** the Admin attempts to delete the customer
- **Then** the system displays an error message
- **And** the customer is not deleted
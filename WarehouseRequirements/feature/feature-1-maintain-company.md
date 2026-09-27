# Feature: Company Maintenance

**Feature ID:** 1

**Branch pattern:** `feature/feature-1-maintain-company`

**Status:** Draft

**Created:** 2026-09-23

**Input:** Maintain company information - Create / Update - including Company ID, Company Name, Address, Phone Number, Email, Website, Business Hours

**Depends On:**

**Related:** feature-2-maintain-warehouse

---

## User Stories

### US-1.1: Create Company Information

**As a** Company Admin

**I want to** create the company's initial information

**So that** the system reflects the company's baseline information


### US-1.2: Update Information

**As a** Company Admin

**I want to** update information about the company

**So that** the company's information remains accurate and up to date. 


---

## Functional Requirements (Rules)

- **FR-001:** System MUST maintain information for one company.
- **FR-002:** System MUST allow an authorized admin to create and update information about the company
- **FR-003:** Company MUST include: Company ID, Company Name, Address, Phone Number, Email, Website, Business Hours
- **FR-004:** System MUST save information about the company
- **FR-005:** System MUST display company information
- **FR-006:** System MUST check that the information is correct and acceptable before allowing it to be saved (Validation)
- **FR-007:** System MUST only allow authorized users to maintain company information 
- **FR-008:** System MUST display error messages if required input fields are invalid or empty 
---

## Key Entities

- **Company**: Buys products in bulk from suppliers and sells to convenience stores. Includes: Company ID, Company Name, Address, Phone Number, Email, Website, Business Hours

- **Warehouse:** Represents a warehouse associated with the company.

---

## Initial Data Model

| Entity | Attribute | Type | Constraints / Notes |
|---|---|---|---|
| Company | CompanyID | Integer | Primary Key, Unique Identifier, Required |
| Company | CompanyName | String | Required |
| Company | Address | String | Required |
| Company | PhoneNumber | String | Required |
| Company | Email | String | Required, Valid Email Format |
| Company | Website | String | Optional, Valid URL Format |
| Company | BusinessHours | String | Required |

### Associations

- **Company &rarr; Warehouse :** One company can have multiple warehouses, and each warehouse refers to the company using Company ID
---

## Gherkin Acceptance Criteria

### US-1.1: Create Company

#### Scenario: Create company information (happy path)

- **Given** no company has been initialized in the system
- **And** the user is an authorized Company Admin
- **When** the Admin enters the company information
- **And** submits the information
- **Then** the system validates the information
- **And** the system saves the company information
- **And** the system displays the company information

#### Scenario: Create company with invalid information (failure / edge)

- **Given** no company has been initialized in the system
- **And** the user is an authorized Company Admin
- **When** the Admin enters invalid company information
- **And** submits the information
- **Then** the system displays an error message
- **And** the invalid information is not saved
- **And** the system requires the user to correct the invalid information before submitting again

### US-1.2: Update Company Information

#### Scenario: Update Company Information (happy path)

- **Given** some company information is outdated or needs to be changed 
- **And** the user is an authorized Company Admin
- **When** the Admin changes existing company information
- **And** submits the changes
- **Then** the system validates the information
- **And** the system saves the company information
- **And** the system displays the updated company information

#### Scenario: Update Company Information with Invalid Information (failure / edge)

- **Given** some company information is outdated or needs to be changed 
- **And** the user is an authorized Company Admin
- **When** the Admin enters invalid information for existing company information 
- **And** submits the changes
- **Then** the system displays an error message
- **And** the invalid information is not saved
- **And** the system requires the user to correct the invalid information before submitting again
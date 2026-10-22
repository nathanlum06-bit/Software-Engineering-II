# Feature: Maintain Location

**Feature ID:** 8
**Branch pattern:** `feature-8-maintain-location`  
**Status:** Draft  
**Created:** 2026-10-20  
**Input:** The owner needs to create, view, update, and deactivate game locations, with each location associated with a home team.
**Related:** feature-4-maintain-team

---

## User Stories

### US-8.1: Create Location

**As a** owner  
**I want to** create a location  
**So that** the organization can designate a location where games can be held.

### US-8.2: View Location

**As an** owner, manager, or viewer  
**I want to** view information about a location  
**So that** I can learn the location's street address, facility type, capacity, etc.

### US-8.3: Update Location

**As an** owner  
**I want to** update information about a location  
**So that** the location's information remains accurate and up to date.

### US-8.4: Deactivate Location

**As an** owner  
**I want to** deactivate a location  
**So that** the organization can stop offering a location that is no longer available while preserving its historical records.

### US-8.5: Reactivate Location

**As an** owner  
**I want to** reactivate a deactivated location  
**So that** the organization can make the location available for future games again.

---

## Functional Requirements

- **FR-001:** The system MUST allow an authorized owner to create a location record.
- **FR-002:** The system MUST assign a unique LocationID to each location record.
- **FR-003:** The system MUST store the location's name, street address, city, state, ZIP code, facility type, capacity, and status.
- **FR-004:** The system MUST require a location name, street address, city, state, ZIP code, facility type, and location status when creating a location record.
- **FR-005:** The system MUST ensure that a location's capacity is greater than or equal to zero.
- **FR-006:** The system MUST allow owners, managers, and viewers to view publicly available location information without requiring authentication.
- **FR-007:** The system MUST allow an authorized owner to update an existing location record.
- **FR-008:** The system MUST validate updated location information before saving changes and MUST reject invalid values.
- **FR-009:** The system MUST allow an authorized owner to deactivate an active location.
- **FR-010:** The system MUST allow an authorized owner to reactivate an inactive location.
- **FR-011:** The system MUST preserve historical records associated with a location when the location is deactivated.
- **FR-012:** The system MUST store each location's status as Active, Inactive, or Under Maintenance.

---

## Key Entities

- **Location:** Represents a facility or venue where games are held. Stores the location's name, address, facility type, capacity, and status.
- **Home Team:** Represents the team associated with a location as its home field. 

---

## Initial Data Model

| Entity | Attribute | Type | Constraints / Notes |
|---|---|---|---|
| Location | LocationID | INT | Primary Key, NOT NULL, UNIQUE |
| Location | LocationName | VARCHAR(100) | NOT NULL |
| Location | TeamID | INT | Foreign Key references Team(TeamID), NOT NULL, UNIQUE, One Location for each Team |
| Location | StreetAddress | VARCHAR(255) | NOT NULL |
| Location | State | VARCHAR(50) | NOT NULL |
| Location | City | VARCHAR(100) | NOT NULL |
| Location | ZipCode | VARCHAR(10) | NOT NULL |
| Location | FacilityType | VARCHAR(50) | NOT NULL, e.g., Gym, Field, Stadium |
| Location | Capacity | INT | Must be greater than or equal to 0 |
| Location | LocationStatus | VARCHAR(30) | NOT NULL, e.g., Active, Inactive, Under Maintenance |

---

## Gherkin AC

### US-8.1: Create Location

#### Scenario: Create Location Successfully (Happy Path)

- **Given** I am an authorized owner
- **When** I click **+ Location**
- **And** I enter all required fields with valid information
- **And** I confirm the information
- **Then** the system creates a new location record
- **And** the location is saved and displayed in the list of locations

#### Scenario: Create Location with Missing or Invalid Information (Bad Path)

- **Given** I am an authorized owner
- **When** I attempt to create a location with missing or invalid information in one or more required fields
- **Then** the system displays an error identifying the missing or invalid information
- **And** the system does not create the location record
- **And** the system allows me to correct the missing or invalid information before submitting again

### US-8.2: View Location

#### Scenario: View Location Successfully (Happy Path)

- **Given** I am visiting the website
- **When** I select a location
- **Then** the system displays the location's publicly available information
- **And** I can view the location's information without logging in

#### Scenario: View Location That Does Not Exist (Bad Path)

- **Given** I am visiting the website
- **When** I attempt to view a location that does not exist
- **Then** the system displays an appropriate message indicating that the requested location could not be found
- **And** the system does not display incorrect or unrelated location information

### US-8.3: Update Player Game Statistics

#### Scenario: Update Player Game Statistics Successfully (Happy Path)

- **Given** I am an authorized manager
- **And** a PlayerGameStats record exists in the system
- **When** I select the statistics record and click **Edit**
- **And** I update the fields with valid information
- **And** I confirm the changes
- **Then** the system saves the updated information for the statistics record
- **And** the updated statistics are displayed in the list of statistics

#### Scenario: Update Player Game Statistics with Invalid Information (Bad Path)

- **Given** I am an authorized manager
- **And** a PlayerGameStats record exists in the system
- **When** I select the statistics record and click **Edit**
- **And** I leave required fields empty or enter invalid data in one or more fields
- **And** I attempt to submit the changes
- **Then** the system displays an error message identifying the missing or invalid information
- **And** the system does not save the invalid changes
- **And** the system allows me to correct the missing or invalid information before submitting again

### US-8.4: Deactivate Player

#### Scenario: Deactivate Player Successfully (Happy Path)

- **Given** I am an authorized owner or manager
- **And** a player exists in the system with an active status
- **When** I select the player and click **Deactivate**
- **And** I confirm the deactivation
- **Then** the system changes the player's status to Inactive
- **And** the player's information remains available for viewing
- **And** the player record can be reactivated at any point by an authorized owner

#### Scenario: Deactivate Player Who Is Already Inactive  (Bad Path)

- **Given** I am an authorized owner or manager
- **And** a player exists in the system with an inactive status
- **When** I attempt to deactivate the player again
- **Then** the system informs me that the player is already inactive
- **And** the player's existing record remains unchanged

### US-8.5: Reactivate Player 

#### Scenario: Reactivate Player Successfully (Happy Path)

- **Given** I am an authorized owner
- **And** a player exists in the system with an inactive status
- **When** I select the team and click **Reactivate**
- **And** I confirm the reactivation
- **Then** the system changes the player status to Active
- **And** the player's record is displayed with the updated status
- **And** the player can be assigned to a team and compete

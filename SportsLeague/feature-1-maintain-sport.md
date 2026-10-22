# Feature: Maintain Sport

**Feature ID:** 1 
**Branch pattern:** `feature-1-maintain-sport`  
**Status:** Draft  
**Created:** 2026-10-20  
**Input:** The owner needs to add, view, update, deactivate, and reactivate sports offered by the organization.
**Related:** None

---
## User Stories

### US-1.1: Create Sport

**As a** owner  
**I want to** create a sport  
**So that** the organization can offer leagues for that sport.

### US-1.2: View Sport

**As a** owner, manager, or viewer  
**I want to** view information about a sport  
**So that** I can understand the sport's rules, equipment requirements, and other relevant details.

### US-1.3: Update Sport

**As a** owner  
**I want to** update information about a sport  
**So that** the sport's information remains accurate and up to date.

### US-1.4: Deactivate Sport

**As a** owner  
**I want to** deactivate a sport  
**So that** the organization can stop offering that sport without permanently deleting its records.

---
## Functional Requirements

- **FR-001:** The system MUST allow an authorized owner to create, update, and deactivate sports.
- **FR-002:** The system MUST store the following information for each sport: Sport ID, Sport Name, Description, Players Per Team, Equipment Required, Playing Area, Scoring Type, and Sport Status.
- **FR-003:** Each Sport ID MUST be unique. The system MUST reject the creation of a sport with a duplicate Sport ID.
- **FR-004:** Each Sport Name MUST be unique. The system MUST reject the creation of a sport with a duplicate Sport Name.
- **FR-005:** Players Per Team MUST be greater than zero.
- **FR-006:** Scoring Type MUST NOT be null or empty and MUST describe how points are scored for the sport.
- **FR-007:** The system MUST deactivate a sport without permanently deleting its record.
- **FR-008:** The system MUST prevent a sport from being deactivated if it is currently in use by an active league or season.
- **FR-009:** The system MUST allow an authorized owner to reactivate a deactivated sport.
- **FR-010:** The system MUST allow new leagues or seasons to be associated with a reactivated sport.
- **FR-011:** The system MUST allow any user to view sport information.
---

## Key Entities

- **Sport:** Represents a sport offered by the organization and stores its rules, requirements, scoring type, and status.
- **League:** Represents a competition organized for a sport. A sport may be associated with multiple leagues.
- **Season:** Represents a specific period of competition within a league.

---
## Initial Data Model

| Entity | Attribute | Type | Constraints / Notes |
|---|---|---|---|
| Sport | SportID | INT | Primary Key, NOT NULL, UNIQUE |
| Sport | SportName | VARCHAR(50) | NOT NULL, UNIQUE |
| Sport | Description | VARCHAR(255) | Optional |
| Sport | PlayersPerTeam | INT | NOT NULL, Must be greater than 0 |
| Sport | EquipmentRequired | VARCHAR(255) | Optional |
| Sport | PlayingArea | VARCHAR(100) | Optional |
| Sport | ScoringType | VARCHAR(50) | NOT NULL |
| Sport | SportStatus | VARCHAR(30) | NOT NULL, ex. Active, Inactive |

## Assumptions
- Publicly available information about sports can be viewed without requiring users to create an account or log in.
- Only authorized owners can create, update, deactivate, or reactivate sports.
- Deactivating a sport preserves its existing records.
- A sport cannot be deactivated while it is in use by an active league or season.
- Reactivating a sport allows it to be used for new leagues and seasons.

---
## Gherkin AC

### US-1.1: Add Sport

#### Scenario: Create Sport Successfully (Happy Path)

- **Given** I am an authorized owner
- **When** I click **+ Sport**
- **And** I enter all required fields with valid information
- **And** I confirm the information
- **Then** the system creates a new sport
- **And** the sport is saved and displayed in the list of sports
- **And** the sport is available for association with leagues

#### Scenario: Create Sport with Invalid Information (Failure / Edge Case)

- **Given** I am an authorized owner
- **When** I click **+ Sport**
- **And** I leave some required fields empty
- **Or** I enter invalid data in one or more fields
- **And** I attempt to submit the form
- **Then** the system displays an error message identifying the invalid or missing information
- **And** the system does not create or save the sport
- **And** the system requires me to correct the invalid or missing information before submitting again

### US-1.2: View Sport

#### Scenario: View Sport Successfully (Happy Path)

- **Given** I am visiting the website
- **When** I click **View Sport**
- **Then** the system displays relevant information about the sport
- **And** I can view the sport's publicly available information without logging in

#### Scenario: View Sport That Does Not Exist (Failure / Edge Case)

- **Given** I am visiting the website
- **When** I attempt to view a sport that does not exist
- **Then** the system displays an appropriate error message
- **And** the system informs me that the requested sport could not be found
- **And** the system does not display incorrect or unrelated sport information

### US-1.3: Update Sport

#### Scenario: Update Sport Successfully (Happy Path)

- **Given** I am an authorized owner
- **And** a sport exists in the system
- **When** I select a sport and click **Edit**
- **And** I update the required fields with valid information
- **And** I confirm the changes
- **Then** the system saves the updated information for the sport
- **And** the updated sport information is displayed in the list of sports
- **And** the sport remains available for association with leagues

#### Scenario: Update Sport with Invalid Information (Bad Path)

- **Given** I am an authorized owner
- **And** a sport exists in the system
- **When** I select a sport and click **Edit**
- **And** I leave some required fields empty
- **Or** I enter invalid data in one or more fields
- **And** I attempt to submit the changes
- **Then** the system displays an error message identifying the invalid or missing information
- **And** the system does not save the sport
- **And** the system requires me to correct the invalid or missing information before submitting again

### US-1.4: Deactivate Sport

#### Scenario: Deactivate Sport Successfully (Happy Path)

- **Given** I am an authorized owner
- **And** a sport exists in the system that is no longer in use
- **When** I select the sport and click **Deactivate**
- **And** I confirm the deactivation
- **Then** the system deactivates the sport
- **And** the sport information remains available for viewing
- **And** the sport can be reactivated at any point by an authorized owner

#### Scenario: Deactivate Sport That Is Still in Use (Bad Path)

- **Given** I am an authorized owner
- **And** a sport exists in the system that is still in use by an active league or season.
- **When** I select the sport and click **Deactivate**
- **And** I attempt to confirm the deactivation
- **Then** the system displays an error message explaining why the sport cannot be deactivated
- **And** the system does not deactivate the sport

### US-1.5: Reactivate Sport 

#### Scenario: Reactivate Sport Successfully (Happy Path)

- **Given** I am an authorized owner
- **And** a sport exists in the system with an inactive status
- **When** I select the sport and click **Reactivate**
- **And** I confirm the reactivation
- **Then** the system changes the sport status to active
- **And** the sport information is displayed in the list of sports
- **And** the sport is available for association with new leagues and seasons
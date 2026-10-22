# Feature: Maintain League

**Feature ID:** 2  
**Branch pattern:** `feature-2-maintain-league`  
**Status:** Draft  
**Created:** 2026-10-20  
**Input:** The owner needs to create, view, update, deactivate, and reactivate the varies leagues offered by the organization. 
**Related:** feature-1-maintain-sport

---

### US-2.1: Create League

**As a** owner  
**I want to** create a league  
**So that** the organization can offer teams to participate in that league.

### US-2.2: View League

**As a** owner, manager, or viewer  
**I want to** view information about a league  
**So that** I can understand the leagues's level, rules, age range, and other relevant details.

### US-2.3: Update League

**As a** owner  
**I want to** update information about a league  
**So that** the leagues's information remains accurate and up to date.

### US-2.4: Deactivate League

**As a** owner  
**I want to** deactivate a league  
**So that** the organization can stop offering that league without permanently deleting its records.

### US-2.5: Reactivate League

**As a** owner  
**I want to** reactivate a deactivated league  
**So that** the organization can offer the league for new seasons and teams again.

---

## Functional Requirements

- **FR-001:** The system MUST allow an authorized owner to create, update, and deactivate leagues.
- **FR-002:** The system MUST store the following information for each league: League ID, League Name, Sport ID, League Level, Age Group, League Status, Estimated Game Duration, and Overtime Duration.
- **FR-003:** Each League ID MUST be unique. The system MUST reject the creation of a league with a duplicate League ID.
- **FR-004:** Each League Name MUST be unique. The system MUST reject a league name that already exists for the same sport.
- **FR-005:** Each league MUST have a corresponding Sport ID that references an existing sport.
- **FR-006:** League Level MUST NOT be null or empty.
- **FR-007:** League Status MUST NOT be null or empty.
- **FR-008:** Estimated Game Duration MUST be greater than 0.
- **FR-009:** Overtime Duration MUST be greater than or equal to 0.
- **FR-010:** The system MUST deactivate a league without permanently deleting its record.
- **FR-011:** The system MUST prevent a league from being deactivated if it is currently associated with an active season.
- **FR-012:** The system MUST allow an authorized owner to reactivate a deactivated league.
- **FR-013:** The system MUST allow any user to view league information.
- **FR-014:** The Estimated Game Duration MUST NOT be null.
- **FR-015:** The Overtime Duration MUST NOT be null.


---

## Key Entities

- **Sport:** Represents a sport offered by the organization and stores its rules, requirements, scoring type, and status.
- **League:** Represents a competition organized for a sport. A sport may be associated with multiple leagues.
- **Season:** Represents a specific period of competition within a league.
- **Team:** Represents a team of players that compete in a league 

---

## Initial Data Model

| Entity | Attribute | Type | Constraints / Notes |
|---|---|---|---|
| League | LeagueID | INT | Primary Key, NOT NULL, UNIQUE |
| League | LeagueName | VARCHAR(100) | NOT NULL, Unique together with SportID |
| League | SportID | INT | Foreign Key references Sport(SportID), NOT NULL |
| League | LeagueLevel | VARCHAR(50) | NOT NULL |
| League | AgeGroup | VARCHAR(50) | Optional |
| League | LeagueStatus | VARCHAR(30) | NOT NULL ex. Active, Inactive|
| League | EstGameDuration | INT | NOT NULL, estimated game duration in minutes, must be greater than 0 |
| League | OvertimeDuration | INT | NOT NULL, overtime duration in minutes, must be greater than or equal to 0 |

## Assumptions

- Publicly available information about leagues can be viewed without requiring users to create an account or log in.
- Only authorized owners can create, update, deactivate, or reactivate leagues.
- Deactivating a league preserves its existing records.
- A league cannot be deactivated while it is associated with an active season.
- Reactivating a league allows it to be used for new seasons and teams.
- Overtime duration may be zero for leagues that do not use overtime.
- League names must be unique only within the same sport.
- League levels and age groups use predefined options rather than unrestricted text.
- Each league must be associated with an existing sport.

---

## Gherkin AC

### US-2.1: Add League

#### Scenario: Create League Successfully (Happy Path)

- **Given** I am an authorized owner
- **When** I click **+ League**
- **And** I enter all required fields with valid information
- **And** I confirm the information
- **Then** the system creates a new league
- **And** the league is saved and displayed in the list of leagues
- **And** the league is available for association with teams

#### Scenario: Create League with Invalid Information (Bad Path)

- **Given** I am an authorized owner
- **When** I click **+ League**
- **And** I leave some required fields empty or enter invalid data in one or more fields
- **Or** a league with the same name already exists for the selected sport
- **And** I attempt to submit the form
- **Then** the system displays an error message identifying the invalid or missing information
- **And** the system does not create or save the league
- **And** the system requires me to correct the invalid or missing information before submitting again

### US-2.2: View League

#### Scenario: View League Successfully (Happy Path)

- **Given** I am visiting the website
- **When** I click **View League**
- **Then** the system displays relevant information about the league
- **And** I can view the league's publicly available information without logging in

#### Scenario: View League That Does Not Exist (Bad Path)

- **Given** I am visiting the website
- **When** I attempt to view a league that does not exist
- **Then** the system displays an appropriate error message
- **And** the system informs me that the requested league could not be found
- **And** the system does not display incorrect or unrelated league information

### US-2.3: Update League

#### Scenario: Update League Successfully (Happy Path)

- **Given** I am an authorized owner
- **And** a league exists in the system
- **When** I select a league and click **Edit**
- **And** I update the required fields with valid information
- **And** I confirm the changes
- **Then** the system saves the updated information for the league
- **And** the updated league information is displayed in the list of leagues
- **And** the league remains available for association with teams

#### Scenario: Update League with Invalid Information (Bad Path)

- **Given** I am an authorized owner
- **And** a league exists in the system
- **When** I select a league and click **Edit**
- **And** I leave some required fields empty or enter invalid data in one or more fields
- **And** I attempt to submit the changes
- **Then** the system displays an error message identifying the invalid or missing information
- **And** the system does not save the changes
- **And** the system requires me to correct the invalid or missing information before submitting again

### US-2.4: Deactivate League

#### Scenario: Deactivate League Successfully (Happy Path)

- **Given** I am an authorized owner
- **And** a league exists in the system that is no longer offered
- **And** the league is not associated with an active season
- **When** I select the league and click **Deactivate**
- **And** I confirm the deactivation
- **Then** the system deactivates the league
- **And** the league information remains available for viewing
- **And** the league can be reactivated at any point by an authorized owner

#### Scenario: Deactivate League That Is Still in Use (Bad Path)

- **Given** I am an authorized owner
- **And** a league exists in the system
- **And** the league is associated with an active season
- **When** I select the league and click **Deactivate**
- **And** I attempt to confirm the deactivation
- **Then** the system displays an error message explaining that the league is associated with an active season
- **And** the system does not deactivate the league

### US-2.5: Reactivate League 

#### Scenario: Reactivate League Successfully (Happy Path)

- **Given** I am an authorized owner
- **And** a league exists in the system with an inactive status
- **When** I select the league and click **Reactivate**
- **And** I confirm the reactivation
- **Then** the system changes the league status to active
- **And** the league information is displayed in the list of leagues
- **And** the league is available for association with new teams

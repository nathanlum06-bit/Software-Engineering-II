# Feature: Maintain Season

**Feature ID:** 3
**Branch pattern:** `feature-3-maintain-season`  
**Status:** Draft  
**Created:** 2026-10-20  
**Input:** The owner needs to create, view, update, and deactivate the varies seasons offered by the organization. 
**Related:** feature-1-maintain-sport, feature-2-maintain-league

---

### US-3.1: Create Season

**As a** owner  
**I want to** create a season  
**So that** the organization can offer a specific time period for a league and sport.

### US-3.2: View Season

**As a** owner, manager, or viewer  
**I want to** view information about a season  
**So that** I can understand its dates, year, status, and associated league.

### US-3.3: Update Season

**As a** owner  
**I want to** update information about a season  
**So that** the season's information remains accurate and up to date.

### US-3.4: Deactivate Season

**As a** owner  
**I want to** deactivate a season  
**So that** the organization can stop offering a season that is canceled or no longer needs to be used.

---

## Functional Requirements

- **FR-001:** The system MUST allow an authorized owner to create, update, deactivate seasons.
- **FR-002:** The system MUST store the following information for each season: Season ID, Season Name, Start Date, End Date, League ID, Season Status, Season Year, and Finals Date.
- **FR-003:** Each Season ID MUST be unique. The system MUST reject the creation of a season with a duplicate Season ID.
- **FR-004:** Each Season Name MUST NOT be null or empty.
- **FR-005:** Each season MUST have a corresponding League ID that references an existing league.
- **FR-006:** Start Date MUST NOT be null.
- **FR-007:** End Date MUST NOT be null and MUST be on or after Start Date.
- **FR-008:** The system MUST deactivate a season without permanently deleting its record.
- **FR-009:** The system MUST prevent a season from being deactivated while it is in progress.
- **FR-010:** The system MUST allow any user to view season information.
- **FR-011:** Season Status MUST NOT be null or empty.
- **FR-012:** The combination of Season Name, Season Year, and League ID MUST be unique.


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
| Season | SeasonID | INT | Primary Key, NOT NULL, UNIQUE |
| Season | SeasonName | VARCHAR(100) | NOT NULL, NOT EMPTY |
| Season | StartDate | DATE | NOT NULL |
| Season | EndDate | DATE | NOT NULL, MUST be on or after StartDate |
| Season | LeagueID | INT | Foreign Key references League(LeagueID), NOT NULL |
| Season | SeasonStatus | VARCHAR(30) | NOT NULL, NOT EMPTY ex. Upcoming, Inprogress, Completed, Deactivated|
| Season | SeasonYear | INT | NOT NULL |
| Season | FinalsDate | DATE | Optional; NULL allowed |
| Season | — | — | Combination of SeasonName, SeasonYear, and LeagueID MUST be unique |

## Assumptions

- Publicly available information about seasons can be viewed without requiring users to create an account or log in.
- Only authorized owners can create, update, deactivate seasons.
- Deactivating a season preserves its existing records.
- A season cannot be deactivated while its in progress
- Season Status Examples: Upcoming, Inprogress, Completed, Deactivated
- A league can have multiple seasons across different years, and multiple seasons can exist within the same year (e.g., Spring 2026 and Fall 2026). Although, the combination of Season Name, Season Year, and League ID MUST be unique.

---

## Gherkin AC

### US-3.1: Add Season

#### Scenario: Create Season Successfully (Happy Path)

- **Given** I am an authorized owner
- **When** I click **+ Season**
- **And** I enter all required fields with valid information
- **And** I confirm the information
- **Then** the system creates a new season
- **And** the league is saved and displayed in the list of seasons
- **And** the season is available for association with teams and games

#### Scenario: Create Season with Invalid Information (Bad Path)

- **Given** I am an authorized owner
- **When** I click **+ Season**
- **And** I leave some required fields empty or enter invalid data in one or more fields
- **Or** a league with the same name already exists for the selected sport
- **And** I attempt to submit the form
- **Then** the system displays an error message identifying the invalid or missing information
- **And** the system does not create or save the season
- **And** the system requires me to correct the invalid or missing information before submitting again

### US-3.2: View Season

#### Scenario: View Season Successfully (Happy Path)

- **Given** I am visiting the website
- **When** I click **View Seasons**
- **Then** the system displays relevant information about each season
- **And** I can view the season's publicly available information without logging in

#### Scenario: View Season That Does Not Exist (Bad Path)

- **Given** I am visiting the website
- **When** I attempt to view a season that does not exist
- **Then** the system displays an appropriate error message
- **And** the system informs me that the requested season could not be found
- **And** the system does not display incorrect or unrelated season information

### US-3.3: Update Season

#### Scenario: Update Season Successfully (Happy Path)

- **Given** I am an authorized owner
- **And** a season exists in the system
- **When** I select a season and click **Edit**
- **And** I update the required fields with valid information
- **And** I confirm the changes
- **Then** the system saves the updated information for the season
- **And** the updated season information is displayed in the list of seasons
- **And** the season remains available for association with teams

#### Scenario: Update Season with Invalid Information (Bad Path)

- **Given** I am an authorized owner
- **And** a season exists in the system
- **When** I select a season and click **Edit**
- **And** I leave some required fields empty or enter invalid data in one or more fields
- **And** I attempt to submit the changes
- **Then** the system displays an error message identifying the invalid or missing information
- **And** the system does not save the changes
- **And** the system requires me to correct the invalid or missing information before submitting again

### US-3.4: Deactivate Season

#### Scenario: Deactivate League Successfully (Happy Path)

- **Given** I am an authorized owner
- **And** a season is NOT in progress
- **When** I select the season and click **Deactivate**
- **And** I confirm the deactivation
- **Then** the system deactivates the season
- **And** the season information remains available for viewing


#### Scenario: Deactivate Season That Is Still in Use (Bad Path)

- **Given** I am an authorized owner
- **And** a season exists in the system
- **And** the season is in progress
- **When** I select the season and click **Deactivate**
- **And** I attempt to confirm the deactivation
- **Then** the system displays an error message explaining that the league is associated with an active time period
- **And** the system does not deactivate the season


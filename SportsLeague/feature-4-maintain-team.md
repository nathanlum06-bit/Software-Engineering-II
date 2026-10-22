# Feature: Maintain Team

**Feature ID:** 4 
**Branch pattern:** `feature-4-maintain-team`  
**Status:** Draft  
**Created:** 2026-10-20  
**Input:** The owner needs to create, view, update, deactivate and reactivate the varies teams competing in the organization. 
**Related:** feature-1-maintain-sport, feature-2-maintain-league, feature-3-maintain-season
---

## User Stories

### US-4.1: Create Team

**As a** owner  
**I want to** create a team  
**So that** the organization can offer a team for players to join.

### US-4.2: View Team

**As a** owner, manager, or viewer  
**I want to** view information about a team  
**So that** I can understand its age group, manager, and home location.

### US-4.3: Update Team

**As a** owner  
**I want to** update information about a team  
**So that** the team's information remains accurate and up to date.

### US-4.4: Deactivate Team

**As a** owner  
**I want to** deactivate a team  
**So that** the organization can stop offering a team that is no longer active while preserving its historical records.

### US-4.5: Reactivate Team

**As a** owner  
**I want to** reactivate a deactivated team  
**So that** the organization can allow the team to participate again.

---

## Functional Requirements

- **FR-001:** The system MUST allow an authorized owner to create, update, deactivate, and reactivate teams.
- **FR-002:** The system MUST store the following information for each team: Team ID, Team Name, Manager ID, Age Group, Location ID, Team Status, and Date Created.
- **FR-003:** Each Team ID MUST be unique. The system MUST reject the creation of a team with a duplicate Team ID.
- **FR-004:** Each Team Name MUST NOT be null or empty.
- **FR-005:** Each team MUST have a corresponding Manager ID that references an existing manager.
- **FR-006:** Each team MUST have a corresponding Location ID that references an existing location.
- **FR-007:** Team Status MUST NOT be null or empty.
- **FR-008:** Date Created MUST NOT be null.
- **FR-009:** The system MUST deactivate a team without permanently deleting its record.
- **FR-010:** The system MUST allow an authorized owner to reactivate a deactivated team.
- **FR-011:** The system MUST allow any user to view team information.
- **FR-012:** The system MUST allow a team to participate in multiple seasons.
- **FR-013:** The system MUST preserve a team's records when the team is deactivated.
- **FR-014:** Each TeamSeasonID MUST be unique. The system MUST reject a TeamSeason record with a duplicate TeamSeasonID.
- **FR-015:** Each TeamSeason record MUST have a TeamID that references an existing Team(TeamID).
- **FR-016:** Each TeamSeason record MUST have a SeasonID that references an existing Season(SeasonID).
- **FR-017:** The system MUST restrict ParticipationStatus to the following values: Registered, Active, Completed, and Withdrawn.
- **FR-018:** The system MUST ensure that each TeamName is unique within its League. The system MUST reject the creation or update of a team if another team in the same league already has that name.


---

## Key Entities

- **Sport:** Represents a sport offered by the organization and stores its rules, requirements, scoring type, and status.
- **League:** Represents a competition organized for a sport. A sport may have multiple leagues.
- **Season:** Represents a specific period of competition within a league. A league may have multiple seasons.
- **Team:** Represents a group of players that can participate in multiple seasons.
- **TeamSeason:** Represents a team's participation in a specific season. Each record connects one team to one season and stores its participation status. 
- **Player:** Represents a person who participates in games as a member of a team.
- **Manager:** Represents a person responsible for managing a team.
- **Location:** Represents a team's home location.

---

## Initial Data Model

| Entity | Attribute | Type | Constraints / Notes |
|---|---|---|---|
| Team | TeamID | INT | Primary Key, NOT NULL, UNIQUE |
| Team | TeamName | VARCHAR(100) | NOT NULL |
| Team | ManagerID | INT | Foreign Key references Manager(ManagerID), NOT NULL |
| Team | AgeGroup | VARCHAR(50) | Optional |
| Team | LocationID | INT | Foreign Key references Location(LocationID), NOT NULL |
| Team | TeamStatus | VARCHAR(30) | NOT NULL |
| Team | DateCreated | DATE | NOT NULL |
| Team | LeagueID | INT | Foreign Key references League(LeagueID), NOT NULL |
| TeamSeason | TeamSeasonID | INT | Primary Key, NOT NULL, UNIQUE |
| TeamSeason | TeamID | INT | Foreign Key references Team(TeamID), NOT NULL |
| TeamSeason | SeasonID | INT | Foreign Key references Season(SeasonID), NOT NULL |
| TeamSeason | ParticipationStatus | VARCHAR(30) | NOT NULL |
| TeamSeason | — | — | Combination of TeamName and LeagueID MUST be unique |

## Assumptions

- Publicly available information about teams can be viewed without requiring users to create an account or log in.
- Only authorized owners can create, update, deactivate, and reactivate teams.
- Deactivating a team preserves its existing records and historical season participation.
- Team Status indicates whether a team is active or inactive.
- ParticipationStatus indicates whether a team is registered for, participating in, or has withdrawn from a specific season.
- Teams can participate in multiple seasons and return for future seasons without creating a new team record.
- A team must reference an existing manager and location when it is created.
- A team cannot be deactivated while it is actively participating in a season that is currently in progress.

---

## Gherkin AC

### US-4.1: Create Team

#### Scenario: Create Team Successfully (Happy Path)

- **Given** I am an authorized owner
- **When** I click **+ Teams**
- **And** I enter all required fields with valid information
- **And** I confirm the information
- **Then** the system creates a new team
- **And** the team is saved and displayed in the list of teams
- **And** the team is available for association with players

#### Scenario: Create Team with Invalid Information (Bad Path)

- **Given** I am an authorized owner
- **When** I attempt to create another team 
- **And** I enter a team with the same name already exists in the selected league
- **Or** I enter missing or invalid information in one or more required fields
- **Then** the system displays an error explaining the dublicating team name or invalid inputs
- **And** the system does not create the team
- **And** the system requires me to correct the invalid, missing, or dublicating information before submitting again

### US-4.2: View Team

#### Scenario: View Team Successfully (Happy Path)

- **Given** I am visiting the website
- **When** I click **View Team**
- **Then** the system displays relevant information about the team
- **And** I can view the team's publicly available information without logging in

#### Scenario: View Team That Does Not Exist (Bad Path)

- **Given** I am visiting the website
- **When** I attempt to view a team that does not exist
- **Then** the system displays an appropriate error message
- **And** the system informs me that the requested team could not be found
- **And** the system does not display incorrect or unrelated team information

### US-4.3: Update Team

#### Scenario: Update Team Successfully (Happy Path)

- **Given** I am an authorized owner
- **And** a team exists in the system
- **When** I select a team and click **Edit**
- **And** I update the required fields with valid information
- **And** I confirm the changes
- **Then** the system saves the updated information for the team
- **And** the updated team information is displayed in the list of teams
- **And** the team remains available for association with players

#### Scenario: Update Team with Invalid Information (Bad Path)

- **Given** I am an authorized owner
- **And** a team exists in the system
- **When** I select a team and click **Edit**
- **And** I leave some required fields empty or enter invalid data in one or more fields
- **And** I attempt to submit the changes
- **Then** the system displays an error message identifying the invalid or missing information
- **And** the system does not save the changes
- **And** the system requires me to correct the invalid or missing information before submitting again

### US-4.4: Deactivate Team

#### Scenario: Deactivate Team Successfully (Happy Path)

- **Given** I am an authorized owner
- **And** a team exists that is no longer participating in an ongoing season
- **When** I select the team and click **Deactivate**
- **And** I confirm the deactivation
- **Then** the system deactivates the team
- **And** the team information remains available for viewing
- **And** the team can be reactivated at any point by an authorized owner

#### Scenario: Deactivate Team That Is Still in Use (Bad Path)

- **Given** I am an authorized owner
- **And** a team exists in the system
- **And** the team is actively participating in a season that is currently in progress
- **When** I select the team and click **Deactivate**
- **And** I attempt to confirm the deactivation
- **Then** the system displays an error message explaining that the team is associated with an active season
- **And** the system does not deactivate the team

### US-4.5: Reactivate Team 

#### Scenario: Reactivate Team Successfully (Happy Path)

- **Given** I am an authorized owner
- **And** a team exists in the system with an inactive status
- **When** I select the team and click **Reactivate**
- **And** I confirm the reactivation
- **Then** the system changes the team status to active
- **And** the team information is displayed in the list of teams
- **And** the team is available for association with new players

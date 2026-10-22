# Feature: Maintain Player 

**Feature ID:** 6 
**Branch pattern:** `feature-6-maintain-player`  
**Status:** Draft  
**Created:** 2026-10-20  
**Input:** The owner or manager needs to create, view, update, deactivate, and reactivate records for each individual player assigned to a team.
**Related:** feature-1-maintain-sport, feature-2-maintain-league, feature-3-maintain-season, feature-4-maintain-team, feature-5-maintain-game

---

## User Stories

### US-6.1: Create Player Record

**As a** owner or manager
**I want to** create a player record  
**So that** I can record statics for a player in each game.

### US-6.2: View Player Record

**As a** owner, manager, or viewer 
**I want to** view a player's record
**So that** I can see information about the player and their game statistics.

### US-6.3: Update Player Record

**As a** owner or manager
**I want to** update a player's record 
**So that** the player's statistics remains accurate and up to date.

### US-6.4: Deactivate Player Record

**As a** owner or manager
**I want to** deactivate a player's record 
**So that** the player is no longer listed as active while their historical records are preserved.

### US-6.5: Reactivate Player Record

**As a** owner or manager
**I want to** reactivate a player's record 
**So that** the player can return to active status and participate in team activities again.

---

## Functional Requirements

### Player Creation and Information

- **FR-001:** The system MUST allow an authorized owner or manager to create a player record.
- **FR-002:** The system MUST store the following information for each player: Player ID, Team ID, First Name, Last Name, Date of Birth, Email, Phone Number, Jersey Number, Position, Registration Date, and Player Status.
- **FR-003:** Each Player ID MUST be unique.
- **FR-004:** Each player MUST reference an existing team through Team ID.
- **FR-005:** First Name MUST NOT be null or empty.
- **FR-006:** Last Name MUST NOT be null or empty.
- **FR-007:** Date of Birth MUST NOT be null.
- **FR-008:** Email and Phone Number MAY be left empty.
- **FR-009:** If a Jersey Number is provided, it MUST be greater than zero.
- **FR-010:** Each Jersey Number MUST be unique within its team.
- **FR-011:** Registration Date MUST NOT be null.
- **FR-012:** Player Status MUST NOT be null or empty. The system MUST support statuses such as Active, Inactive, Injured, and Suspended.

### Player Record Management

- **FR-013:** The system MUST allow an authorized owner or manager to update a player's record.
- FR-014: The system MUST allow any user to view publicly available player information and statistics without requiring authentication.
- **FR-015:** The system MUST allow an authorized owner or manager to update a player's status.
- **FR-016:** The system MUST preserve a player's record when the player's status changes to Inactive, Injured, or Suspended.

---

## Key Entities

- **Player:** Represents a person registered to participate on a team. Stores the player's personal information, team assignment, jersey number, position, registration date, and status.
- **Team:** Represents a group of players participating in a league. Each player is associated with a team through Team ID.
- **Game:** Represents a competition between two teams within a season. A player may participate in multiple games.
- **PlayerGameStats:** Represents a player's statistics for a specific game. It connects a player to a game and stores statistics recorded for that player's performance. Overall and seasonal statistics can be deduced from each player's statistics for each game 

---

## Initial Data Model

| Entity | Attribute | Type | Constraints / Notes |
|---|---|---|---|
| Player | PlayerID | INT | Primary Key, NOT NULL, UNIQUE |
| Player | TeamID | INT | Foreign Key references Team(TeamID), NOT NULL |
| Player | FirstName | VARCHAR(50) | NOT NULL |
| Player | LastName | VARCHAR(50) | NOT NULL |
| Player | DateOfBirth | DATE | NOT NULL |
| Player | Email | VARCHAR(255) | Optional |
| Player | PhoneNumber | VARCHAR(20) | Optional |
| Player | JerseyNumber | INT | Must be greater than 0, Unique per team |
| Player | Position | VARCHAR(50) | Optional |
| Player | RegistrationDate | DATE | NOT NULL |
| Player | PlayerStatus | VARCHAR(30) | NOT NULL, e.g., Active, Inactive, Injured, Suspended |

## Assumptions

- Publicly available information about players can be viewed without requiring users to create an account or log in.
- Only authorized owners and managers can create, update, deactivate, activate players.
- Player Status indicates whether a player is Active, Inactive, Injured, Suspended .
- Each player belongs to one team. 

---

## Gherkin AC

### US-6.1: Create Player Record

#### Scenario: Create Player Record Successfully (Happy Path)

- **Given** I am an authorized owner or manager
- **When** I select a team and click **+ Players**
- **And** I enter all required fields with valid information
- **And** I confirm the information
- **Then** the system creates a new player
- **And** the player is saved and displayed in the list of players


#### Scenario: Create Player Record with Invalid Information (Bad Path)

- **Given** I am an authorized owner or manager
- **When** I attempt to create another player 
- **And** I enter missing or invalid information in one or more required fields
- **Then** the system displays an error explaining the invalid inputs
- **And** the system does not create the player
- **And** the system requires me to correct the invalid or missing information before submitting again

### US-6.2: View Player

#### Scenario: View Player Successfully (Happy Path)

- **Given** I am visiting the website
- **When** I click **View Player**
- **Then** the system displays relevant information about the player
- **And** I can view the player's publicly available information without logging in

#### Scenario: View Player That Does Not Exist (Bad Path)

- **Given** I am visiting the website
- **When** I attempt to view a player that does not exist
- **Then** the system displays an appropriate error message
- **And** the system informs me that the requested player could not be found
- **And** the system does not display incorrect or unrelated player information

### US-6.3: Update Player

#### Scenario: Update Player Successfully (Happy Path)

- **Given** I am an authorized owner or manager
- **And** a player exists in the system
- **When** I select a player and click **Edit**
- **And** I update the required fields with valid information
- **And** I confirm the changes
- **Then** the system saves the updated information for the player
- **And** the updated player information is displayed in the list of players

#### Scenario: Update Player with Invalid Information (Bad Path)

- **Given** I am an authorized owner or manager
- **And** a player exists in the system
- **When** I select a player and click **Edit**
- **And** I leave some required fields empty or enter invalid data in one or more fields
- **And** I attempt to submit the changes
- **Then** the system displays an error message identifying the invalid or missing information
- **And** the system does not save the changes
- **And** the system requires me to correct the invalid or missing information before submitting again

### US-6.4: Deactivate Player

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

### US-6.5: Reactivate Player 

#### Scenario: Reactivate Player Successfully (Happy Path)

- **Given** I am an authorized owner
- **And** a player exists in the system with an inactive status
- **When** I select the team and click **Reactivate**
- **And** I confirm the reactivation
- **Then** the system changes the player status to Active
- **And** the player's record is displayed with the updated status
- **And** the player can be assigned to a team and compete

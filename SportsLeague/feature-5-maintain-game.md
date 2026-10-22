# Feature: Maintain Game

**Feature ID:** 5 
**Branch pattern:** `feature-5-maintain-game`  
**Status:** Draft  
**Created:** 2026-10-20  
**Input:** The owner needs to create, view, and update the games scheduled for a season, with 18 games specified by the stakeholder. 
**Related:** feature-1-maintain-sport, feature-2-maintain-league, feature-3-maintain-season, feature-4-maintain-team

---

## User Stories

### US-5.1: Create Game

**As a** owner 
**I want to** create a game  
**So that** two teams can compete against each other. 

### US-5.2: View Game

**As a** owner, manager, or viewer 
**I want to** view a game  
**So that** I can see information and statistics about the game.

### US-5.3: Update Game

**As a** owner
**I want to** update a game's information 
**So that** the game's information remains accurate and up to date.


---

## Functional Requirements

### Game Creation and Information

- **FR-001:** The system MUST allow an authorized owner to create a game.
- **FR-002:** The system MUST store the following information for each game: Game ID, Season ID, Home Team ID, Away Team ID, Game Date, Location ID, Game Type, Home Team Score, Away Team Score, Game Duration, Overtime Played, Timeouts Used, and Game Status.
- **FR-003:** Each Game ID MUST be unique.
- **FR-004:** Each game MUST reference an existing season through Season ID.
- **FR-005:** Each game MUST reference existing teams through Home Team ID and Away Team ID.
- **FR-006:** The Home Team ID and Away Team ID MUST reference different teams.
- **FR-007:** Each game MUST reference an existing location through Location ID.
- **FR-008:** Game Date MUST NOT be null.
- **FR-009:** Game Type MUST NOT be null or empty. The system MUST support game types such as Regular Season, Playoff, and Final.
- **FR-010:** Game Duration MUST be greater than zero.
- **FR-011:** Overtime Played MUST NOT be null and MUST default to false when a game is created.
- **FR-012:** Timeouts Used MUST be greater than or equal to zero.
- **FR-013:** Game Status MUST NOT be null or empty. The system MUST support statuses such as Scheduled, In Progress, Completed, and Canceled.

### Game Scores and Status

- **FR-014:** The system MUST allow game scores to remain unset until scoring information is recorded.
- **FR-015:** When a Home Team Score or Away Team Score is entered, the score MUST be greater than or equal to zero.
- **FR-016:** The system MUST allow an authorized owner to update a game's information.
- **FR-017:** The system MUST allow users to view game information.
- **FR-018:** The system MUST store whether overtime was played for each game.
- **FR-019:** The system MUST store the number of timeouts used during each game.

### Referee Assignments

- **FR-020:** The system MUST allow referees to be assigned to games.
- **FR-021:** Each GameReferee record MUST reference an existing game through Game ID.
- **FR-022:** Each GameReferee record MUST reference an existing referee through Referee ID.
- **FR-023:** The combination of Game ID and Referee ID MUST be unique in GameReferee.
- **FR-024:** Each GameReferee record MUST have a Referee Role that is not null or empty.

---

## Key Entities

- **Game:** Represents a scheduled or completed competition between two teams within a season. It stores the game date, location, game type, scores, duration, overtime information, timeouts used, and game status.
- **Season:** Represents a specific period of competition within a league. A season can contain multiple games.
- **Team:** Represents a group of players participating in a league. Each game has a home team and an away team, which must be different teams.
- **Location:** Represents the physical location where a game takes place.
- **GameReferee:** Represents the assignment of a referee to a game. It connects a game to a referee and stores the referee's role for that game.
- **Referee:** Represents an official responsible for officiating a game.

---

## Initial Data Model

| Entity | Attribute | Type | Constraints / Notes |
|---|---|---|---|
| Game | GameID | INT | Primary Key, NOT NULL, UNIQUE |
| Game | SeasonID | INT | Foreign Key references Season(SeasonID), NOT NULL |
| Game | HomeTeamID | INT | Foreign Key references Team(TeamID), NOT NULL |
| Game | AwayTeamID | INT | Foreign Key references Team(TeamID), NOT NULL, Must differ from HomeTeamID |
| Game | GameDate | DATETIME | NOT NULL |
| Game | LocationID | INT | Foreign Key references Location(LocationID), NOT NULL |
| Game | GameType | VARCHAR(30) | NOT NULL, e.g., Regular Season, Playoff, Final |
| Game | HomeTeamScore | INT | Must be greater than or equal to 0, Optional until scored |
| Game | AwayTeamScore | INT | Must be greater than or equal to 0, Optional until scored |
| Game | GameDuration | INT | Duration in minutes, Must be greater than 0 |
| Game | OvertimePlayed | BOOLEAN | NOT NULL, DEFAULT FALSE |
| Game | TimeoutsUsed | INT | Must be greater than or equal to 0 |
| Game | GameStatus | VARCHAR(30) | NOT NULL, e.g., Scheduled, In Progress, Completed, Canceled |
| GameReferee | GameID | INT | Primary Key (Composite), Foreign Key references Game(GameID), NOT NULL |
| GameReferee | RefereeID | INT | Primary Key (Composite), Foreign Key references Referee(RefereeID), NOT NULL |
| GameReferee | RefereeRole | VARCHAR(50) | NOT NULL, specific role |

## Assumptions

- Publicly available information about games can be viewed without requiring users to create an account or log in.
- Only authorized owners can create and update games.
- Game Status indicates whether a game is Scheduled, In Progress, Completed, or Canceled.
- Each game belongs to one season and has exactly two different teams: a home team and an away team.
- Both teams must be registered to participate in the season associated with the game.
- Game scores may remain unset until scoring information is recorded, and scores cannot be negative.
- Game duration, overtime information, and timeouts used are recorded as part of the game's statistics.
- Each game may have one or more assigned referees, and a referee cannot be assigned to the same game more than once.
- The stakeholder has specified a schedule consisting of 18 games for the season.

---

## Gherkin AC

### US-5.1: Create Game

#### Scenario: Create Game Successfully (Happy Path)

- **Given** I am an authorized owner
- **When** I click **+ Game**
- **And** I enter all required fields with valid information
- **And** I confirm the information
- **Then** the system creates a new game
- **And** the game is saved and displayed in the list of games
- **And** the game is associated with the selected season, home team, and away team

#### Scenario: Create Game with Missing or Invalid Information (Bad Path)

- **Given** I am an authorized owner
- **When** I attempt to create a game with missing or invalid information in one or more required fields
- **Then** the system displays an error identifying the missing or invalid information
- **And** the system does not create the game
- **And** the system allows me to correct the information before submitting again

### US-5.2: View Game

#### Scenario: View Game Successfully (Happy Path)

- **Given** I am visiting the website
- **When** I select **View Game**
- **Then** the system displays relevant information about the game
- **And** I can view the game's publicly available information without logging in

#### Scenario: View Game That Does Not Exist (Bad Path)

- **Given** I am visiting the website
- **When** I attempt to view a game that does not exist
- **Then** the system displays an appropriate error message
- **And** the system informs me that the requested game could not be found
- **And** the system does not display incorrect or unrelated game information

### US-5.3: Update Game

#### Scenario: Update Game Successfully (Happy Path)

- **Given** I am an authorized owner
- **And** a game exists in the system
- **When** I select a game and click **Edit**
- **And** I update the required fields with valid information
- **And** I confirm the changes
- **Then** the system saves the updated information for the game
- **And** the updated game information is displayed in the list of games
- **And** the game remains available for two teams

#### Scenario: Update Game with Invalid Information (Bad Path)

- **Given** I am an authorized owner
- **And** a game exists in the system
- **When** I select a game and click **Edit**
- **And** I leave some required fields empty or enter invalid data in one or more fields
- **And** I attempt to submit the changes
- **Then** the system displays an error message identifying the invalid or missing information
- **And** the system does not save the changes
- **And** the system requires me to correct the invalid or missing information before submitting again
# Feature: Maintain Player Game Statistics

**Feature ID:** 7  
**Branch pattern:** `feature-7-maintain-stats`  
**Status:** Draft  
**Created:** 2026-10-20  
**Input:** The manager needs to create, view, and update individual player game statistics for every game.
**Related:** feature-1-maintain-sport, feature-2-maintain-league, feature-3-maintain-season, feature-4-maintain-team, feature-5-maintain-game, feature-6-maintain-player

---

## User Stories

### US-7.1: Create Player Game Statistics

**As a** manager 
**I want to** record statistics for a player's performance in a specific game
**So that** I can document and track the player's performance.

### US-7.2: View Player Statistics

**As a** owner, manager, viewer
**I want to** view a player's game, seasonal, and overall statistics   
**So that** I can evaluate and track the player's performance over time.

### US-7.3: Update Player Game Statistics

**As a** manager
**I want to** update a player's statistics for a specific game
**So that** the recorded statistics remain accurate and up to date.

---

## Functional Requirements

- **FR-001:** The system MUST allow an authorized manager to create a statistics record for a player in a specific game.
- **FR-002:** The system MUST assign a unique PlayerGameStatsID to each statistics record.
- **FR-003:** Each statistics record MUST reference an existing player through PlayerID.
- **FR-004:** Each statistics record MUST reference an existing game through GameID.
- **FR-005:** The system MUST store MinutesPlayed, PointsScored, Assists, Penalties, and Fouls for each statistics record.
- **FR-006:** MinutesPlayed MUST be greater than or equal to zero.
- **FR-007:** PointsScored, Assists, Penalties, and Fouls MUST each be greater than or equal to zero.
- **FR-008:** The system MAY store additional sport-specific statistics in SportSpecificStats.
- **FR-009:** The system MUST allow an authorized manager to update an existing statistics record.
- **FR-010:** The system MUST validate updated statistics before saving changes and MUST reject invalid values.
- **FR-011:** The system MUST allow owners, managers, and viewers to view publicly available player statistics without requiring authentication.
- **FR-012:** The system MUST allow users to view a player's statistics for an individual game.
- **FR-013:** The system MUST calculate or display a player's overall statistics using the player's recorded game statistics.
- **FR-014:** The system MUST calculate and display a player's seasonal statistics by aggregating the player's individual game statistics for the selected season.
- **FR-015:** The system MUST calculate and display a player's overall statistics by aggregating the player's individual game statistics across all seasons.

---

## Key Entities

- **PlayerGameStats:** Represents a player's performance statistics for a specific game. Stores minutes played, points scored, assists, penalties, fouls, and optional sport-specific statistics. Each record is associated with one player and one game.
- **Player:** Represents an individual player registered to a team. Each player can have statistics records for multiple games.
- **Game:** Represents a competition between two teams within a season. Each game can have statistics records for multiple players.

---

## Initial Data Model

| Entity | Attribute | Type | Constraints / Notes |
|---|---|---|---|
| PlayerGameStats | PlayerGameStatsID | INT | Primary Key, NOT NULL, UNIQUE |
| PlayerGameStats | PlayerID | INT | Foreign Key references Player(PlayerID), NOT NULL |
| PlayerGameStats | GameID | INT | Foreign Key references Game(GameID), NOT NULL |
| PlayerGameStats | MinutesPlayed | DECIMAL(5,2) | Must be greater than or equal to 0 |
| PlayerGameStats | PointsScored | INT | Must be greater than or equal to 0 |
| PlayerGameStats | Assists | INT | Must be greater than or equal to 0 |
| PlayerGameStats | Penalties | INT | Must be greater than or equal to 0 |
| PlayerGameStats | Fouls | INT | Must be greater than or equal to 0 |
| PlayerGameStats | SportSpecificStats | TEXT | Optional, could become a separate entity later |

## Assumptions
- Publicly available player game statistics can be viewed without requiring users to create an account or log in.
- Only authorized managers can create and update Player Game Statistics records.
- Player Game Statistics records are associated with an existing player and an existing game.

---

## Gherkin AC

### US-7.1: Create Player Game Statistics

#### Scenario: Create Player Game Statistics Successfully (Happy Path)

- **Given** I am an authorized manager
- **When** I select a player and click **+ Player Game Statistic**
- **And** I select the game associated with the player's statistics
- **And** I enter all required fields with valid information
- **And** I confirm the information
- **Then** the system creates a new PlayerGameStats record
- **And** the statistics record is saved and displayed in the list of statistics

#### Scenario: Create Player Game Statistics with Missing or Invalid Information (Bad Path)

- **Given** I am an authorized manager
- **When** I attempt to create a statistics record with missing or invalid information in one or more required fields
- **Then** the system displays an error identifying the missing or invalid information
- **And** the system does not create the statistics record
- **And** the system allows me to correct the information before submitting again

### US-7.2: View Player Game Statistic

#### Scenario: View Player Game Statistics Successfully (Happy Path)

- **Given** I am visiting the website
- **When** I select a player's statistics for a specific game
- **Then** the system displays the player's publicly available statistics for that game
- **And** I can view the statistics without logging in

#### Scenario: View Player Game Statistics That Do Not Exist (Bad Path)

- **Given** I am visiting the website
- **When** I attempt to view a player game statistics record that does not exist
- **Then** the system displays an appropriate message indicating that the requested statistics could not be found
- **And** the system does not display incorrect or unrelated statistics

### US-7.3: Update Player Game Statistics

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
# Feature: Maintain Users

**Feature ID:** 9
**Branch pattern:** `feature-9-maintain-users`  
**Status:** Draft  
**Created:** 2026-10-20  
**Input:** The owner needs to create, view, update, and deactivate user accounts for managers, referees, and viewers.
**Related:** feature-1-maintain-sport, feature-2-maintain-league, feature-3-maintain-season, feature-4-maintain-team, feature-5-maintain-game, feature-6-maintain-player, feature-7-maintain-stats
---

## User Stories

### US-N.1: Short title

**As a** <role>  
**I want to** <capability>  
**So that** <benefit>

**Priority:** P1  
**Independent test:** <How to verify this story alone>

---

## Functional Requirements

- **FR-001:** System MUST <requirement>
- **FR-002:** System MUST <requirement>

---

## Key Entities

- **Entity:** Short description and relationships in plain language
- **Entity:** Short description and relationships in plain language

---

## Initial Data Model

| Entity | Attribute | Type | Constraints / Notes |
|---|---|---|---|
| User | UserID | INT | Primary Key, NOT NULL, UNIQUE |
| User | FirstName | VARCHAR(50) | NOT NULL |
| User | LastName | VARCHAR(50) | NOT NULL |
| User | Role | VARCHAR(30) | NOT NULL, e.g., Admin, Referee, Manager, General User |
| User | Email | VARCHAR(255) | NOT NULL, UNIQUE |
| User | PasswordHash | VARCHAR(255) | NOT NULL, Store hashed password, never plaintext |
| User | PhoneNumber | VARCHAR(20) | Optional |
| User | AccountStatus | VARCHAR(30) | NOT NULL, e.g., Active, Inactive, Suspended |
| User | DateCreated | DATETIME | NOT NULL |
| Referee | RefereeID | INT | Primary Key, NOT NULL, UNIQUE |
| Referee | UserID | INT | Foreign Key references User(UserID), NOT NULL, UNIQUE |
| Referee | CertifiedLevel | VARCHAR(50) | Optional |
| TeamManager | ManagerID | INT | Primary Key, NOT NULL, UNIQUE |
| TeamManager | UserID | INT | Foreign Key references User(UserID), NOT NULL, UNIQUE |
| Owner | OwnerID | INT | Primary Key, NOT NULL, UNIQUE |
| Owner | UserID | INT | Foreign Key references User(UserID), NOT NULL, UNIQUE |

---

## Gherkin AC

### US-N.1: Short title

#### Scenario: Descriptive name (happy path)

- **Given** <starting state>
- **When** <action>
- **Then** <observable result>
- **And** <additional result if needed>

#### Scenario: Descriptive name (failure / edge case)

- **Given** <starting state>
- **When** <action>
- **Then** <expected result>
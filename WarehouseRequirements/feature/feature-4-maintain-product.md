# Feature: <Human-readable title>
**Feature ID:** N
**Branch pattern:** `feature/N-short-name`
**Status:** Draft
**Created:** YYYY-MM-DD
**Input:** One sentence .md)
**Related:** optional links to ADRs or reference docs
---
## User Stories
### US-N.1: Short title
**As a** <role>
**I want to** <capability>
**So that** <benefit>
**Priority:** P1
**Independent test:** <how to verify this story alone, in one
sentence>
**Acceptance scenarios:** see ### US-N.1 under Acceptance
Criteria
### US-N.2: Short title
**As a** <role>
**I want to** <capability>
**So that** <benefit>
**Priority:** P1
**Independent test:** <how to verify this story alone, in one
sentence>
**Acceptance scenarios:** see ### US-N.2 under Acceptance
Criteria
---
## Requirements
### Functional Requirements
- **FR-001**: System MUST
- **FR-003**:
---
## Assumptions
- What already exists (e.g. Feature 1 auth is on `dev`)
- What you are deliberately not building yet
---
## Edge Cases
- Empty required field
- Cross-user access
- Duplicate / invalid input
---
## Success Criteria
- **SC-001**: Every Gherkin scenario has at least one automated
test before merge
- **SC-002**: <measurable outcome for this feature>
---
## Key Entities
- **Entity**: short description; relationships in plain language
- **Entity**: ` | |
### Associations (if known)
- Short title (same as the story)
#### Scenario: Descriptive name (happy path)
* **Given** <starting state>
* **When** <action>
* **Then** <observable result>
* **And** <extra result if needed>
#### Scenario: Descriptive name (failure / edge)
* **Given**
* **Then** Short title (same as the story)
#### Scenario: Descriptive name (happy path)
* **Given**
* **Then**
* **When**

# Feature Church Member Management: 

**Feature ID :** 1

**Branch pattern:** feature/1-Church-Member-System

**Status:** Planning 

**Created:** 2026-11-10

**Input:** Church faculty maintain (add, delete, edit) contact information of current members in the church. Church members can request maintenance on their contact information.

**Depends on:** None 

**Priority:** P1

**Independent test:** Each entry is unique without dublicate data and all input fields contain valid inputs

**Acceptance scenarios:** see ### US- under Acceptance Criteria

## User Stories:
### US-1:
**As a** staff member of the church, **I want to** have the ability to gather contact information of new members **so that** I can keep track what class or service they are attending and can follow up on them.

**Priority:** P1

### US-2:
**As a** new attendee of the church, **I want to** able to add my contact information **so that** the church knows I'm attending weekly and can reach out to me if needed.

**Priority:** P1

### US-3:
**As a** staff member of the church, **I want to** be able to update contact information of current and returning members **so that** we have the most up to date information about each member.

**Priority:** P1

### US-4 
**As a** current member of the church, **I want to** be able to request updates to my contact information **so that** I can change what class or service I'm currently attending. Additionally, I can change my address, phone number, and email if needed 

### US-5:
**As a** staff member of the church, **I want to** be able to delete contact information of former members **so that** we keep the system as precise and concise as possible. Additionally, we must protect the privacy of the former memebers.

**Priority:** P1

### US-6 
**As a** former member of the church, **I want to** be able to request that the church deletes my contact information **so that** if I choose to no longer attend for whatever reason. 

**Priority:** P1



---

## Requirements

### Functional Requirements (MUST -> SHOULD -> COULD)

**FR-001**: (MUST)
Contact Information Data:
- First Name
- Last Name
- Address
- Phone Number
- Email 
- Van Route (If Applicable or Needed)
- Class or Service currently attending
- Public School (If Applicable)
- "Have you been baptized before?"
- "Do you any family that also attends this church?"
- "When was your first day attend this church?"
- Active or Inactive Member

**FR-002**: (MUST) Add, Update, and Delete Contact Information

**FR-003**: (MUST) Faculty must login with credentials in order to use application 

**FR-004**: (MUST) Church faculty have full access to all church members' contact information

**FR-005**: (MUST) Church members have limited access, only being able to see and request maintenance on their contact information

**FR-006**: (MUST) Max load: 20 - 50 users at once

**FR-007**: (SHOULD) Validate and Check for Edge Cases

**FR-008**: (SHOULD) Application should be live and running only during reasonable times

**FR-009**: (SHOULD) Contact information data can be formatted into a spread sheet

**FR-010**: (SHOULD) Application can be used anywhere during its running time

**FR-011**: (COULD) Application has a spanish mode

**FR-012**: (COULD) Run on both Windows and Mac

**FR-013**: (COULD) Application could have a mobile app version

## Initial Data Model

**Member**:
- firstName: String
- lastName: String
- address: String
- phoneNumber: String
- email: String
- vanRoute: String
- currentClassOrService: String
- publicSchool: String
- hasBeenBaptized: Boolean
- hasFamilyAtChurch: Boolean
- firstAttendanceDate: Date
- isActiveMember: Boolean

**Staff**:
- staffId: String
- password: String

## Gherkin AC
### Scenario: Staff Member Side

**Given** I am a church staff member 

**When** I start the application, I should see a login page

**And** once a enter a valid church ID and password

**Then** I should have full access any church members information

### Scenario: New Member

**Given** I am a new member

**When** I start the application I should see a guest page 

**And** once a click on new member, I should be prompted to enter my contact information

**Then** my information is gathered and collected into the system

### Scenario: Invalid or Empty Field

**Given** I am a new member

**And** forget to fill in a necessary field, I should get an error telling what I forgot to fill in

**And** I mistype an invalid input, I should get an error tell me thats a invalid answer and to please try again

**Then** I can enter all my information correctly

### Scenario: Member Request

**Given** I am a current member

**When** I start the application I should see a request page 

**And** once a click on request change, I should be prompted to enter what I desire to change about my contact information

**Then** the request is pending until a staff member validates it 


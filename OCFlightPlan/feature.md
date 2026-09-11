# OCFlightPlan — System Features

## Epic user story

As a student, when I want to do activities that prepare me to get a job when I graduate, I want a checklist of tasks and events I can do each semester I am in school so that I am prepared to successfully apply for a job.

## Product decisions

These choices shape the features below:

| Decision | Choice |
|---|---|
| **Who uses the system** | Students, teachers, faculty, admins, and career advisors |
| **How the checklist is split** | By class year (first-year, sophomore, junior, senior), not one combined list |
| **What is on a year's checklist** | A general checklist for every student in that year, plus major-specific objectives for the student's major |

## Roles

| Role | Who they are | What they do in OCFlightPlan |
|---|---|---|
| **Student** | The person working the flight plan | Completes this year's general tasks, major-specific objectives, and related events so they can apply for a job |
| **Career advisor** | Career services staff | Owns the general year checklists and career events; reviews student progress and advises |
| **Faculty** | Program / department instructors | Own major-specific objectives by year; review progress for students in that major |
| **Teacher** | Course instructors | See progress for students in their classes and point them to the right year's tasks and events |
| **Admin** | System administrator | Manages accounts, roles, and the current academic year |

---

# Feature 1: Year-based checklist (general + major)

The student sees one checklist for their current class year. That year is split into:

1. **General checklist** — same for every student in that year
2. **Major-specific objectives** — only for their declared major

First-year, sophomore, junior, and senior lists are separate.

### User stories

- **US-1.1** As a student, I want to open the checklist for my current class year so that I work only the tasks meant for this year of school.
- **US-1.2** As a student, I want first-year, sophomore, junior, and senior checklists kept separate so that I do not mix other years' work with this year's.
- **US-1.3** As a student, I want the general checklist and my major-specific objectives shown as two sections on my year's page so that I can tell school-wide tasks from major requirements.
- **US-1.4** As a first-year student, I want a first-year general checklist (for example explore options, join campus activities, start a basic resume) so that I begin job prep early.
- **US-1.5** As a sophomore, I want a sophomore general checklist so that I build skills and experience before internships.
- **US-1.6** As a junior, I want a junior general checklist focused on internships and career events so that I gain experience before graduation.
- **US-1.7** As a senior, I want a senior general checklist focused on applying for jobs so that I can apply successfully before I graduate.
- **US-1.8** As a student with a declared major, I want to see and check off this year's major-specific objectives so that I complete program-specific job-prep work.
- **US-1.9** As a student, I want each item to include a short description and why it matters so that I understand how it helps me get a job.
- **US-1.10** As a student, I want my checklist to switch when my class year changes so that I start the next year's list instead of staying on last year's.

---

# Feature 2: Task and objective completion

The student marks general tasks and major-specific objectives as done and can see remaining work for this year.

### User stories

- **US-2.1** As a student, I want to check off general tasks and major-specific objectives I have completed so that I can keep track of what is left this year.
- **US-2.2** As a student, I want to uncheck an item if I marked it done by mistake so that my checklist stays accurate.
- **US-2.3** As a student, I want to see progress for this year's general checklist and major-specific objectives separately so that I know if I am behind on either part.
- **US-2.4** As a student, I want completed items to stay saved when I leave and come back so that I do not lose my progress.

---

# Feature 3: Events and campus activities

The student can find career-related events (career fairs, workshops, employer visits, mock interviews) that fit their year and count toward the checklist.

### User stories

- **US-3.1** As a student, I want to see upcoming career events that fit my class year so that I can attend activities that help me get hired.
- **US-3.2** As a student, I want event details (date, time, location, and what to bring) so that I can show up prepared.
- **US-3.3** As a student, I want to mark that I attended an event so that it can count toward this year's checklist.
- **US-3.4** As a student, I want to filter events by type (career fair, workshop, info session) so that I can find activities that match my goals.

---

# Feature 4: Job-application readiness

The student can see whether they are ready to apply, based on this year's general tasks and major-specific objectives—not only a raw list of boxes.

### User stories

- **US-4.1** As a student, I want a simple readiness summary that includes both general tasks and major-specific objectives so that I know if I am prepared to apply.
- **US-4.2** As a student, I want to see which high-priority items are still incomplete so that I can finish the work that matters most for applications.
- **US-4.3** As a senior, I want my year's checklist to include application steps (resume, cover letter, references, practice interviews) so that I do not miss them before I graduate.

---

# Feature 5: Student account and profile

The student has an account so class year, major, and progress belong to them.

### User stories

- **US-5.1** As a student, I want to create an account and sign in so that my checklist is private and saved to me.
- **US-5.2** As a student, I want to set my class year (first-year, sophomore, junior, or senior) so that I get the correct year's checklist.
- **US-5.3** As a student, I want to set my expected graduation term so that staff and I share the same timeline.
- **US-5.4** As a student, I want to set or change my major so that the correct major-specific objectives appear for my year.
- **US-5.5** As a student, I want major-specific objectives to update if I change majors so that I am not stuck with the old program's list.

---

# Feature 6: Publishing checklists and events (staff)

Career advisors maintain the general checklist and events for each class year. Faculty maintain major-specific objectives for their major, also by class year.

### User stories

- **US-6.1** As a career advisor, I want to add, edit, and remove general tasks for a chosen class year so that each year has its own current school-wide plan.
- **US-6.2** As a career advisor, I want to mark a general task as required or optional so that students know what is essential for job readiness.
- **US-6.3** As a career advisor, I want to publish career events (date, time, location, type, and which class years they apply to) so that students can find activities to attend.
- **US-6.4** As a career advisor, I want to attach an event to a checklist item so that attending can count as completing that item.
- **US-6.5** As a faculty member, I want to add, edit, and remove major-specific objectives for my major by class year so that students in that program get relevant work for their year.
- **US-6.6** As a teacher, I want to view the published year checklists and events so that I can point my class to the right tasks.

---

# Feature 7: Progress for advisors, faculty, and teachers

Staff can see whether students are completing this year's general checklist and major-specific objectives so they can help before graduation.

### User stories

- **US-7.1** As a career advisor, I want to view a student's year, general progress, and major-specific progress so that I can advise them on what to do next.
- **US-7.2** As a career advisor, I want a list of students who are behind on their year's checklist so that I can reach out before they fall further off track.
- **US-7.3** As a faculty member, I want to see how students in my major are doing on this year's major-specific objectives so that I can support my program's students.
- **US-7.4** As a teacher, I want to see progress for students in my classes (this year's general and major-specific items) so that I can remind them about career-prep tasks and events.
- **US-7.5** As a student, I want only authorized staff (career advisors, faculty, teachers, admins) to see my progress so that my checklist stays reasonably private.

---

# Feature 8: Administration

Admins keep accounts, roles, and the current academic year correct.

### User stories

- **US-8.1** As an admin, I want to create and deactivate user accounts so that only current OC students and staff can use the system.
- **US-8.2** As an admin, I want to assign roles (student, career advisor, faculty, teacher, admin) so that people only get the access they need.
- **US-8.3** As an admin, I want to set the current academic year so that students are pointed at the right year's checklist.
- **US-8.4** As an admin, I want to restore access if someone is locked out so that students and staff are not blocked from the flight plan.

---

# Traceability

| Feature | Supports the epic by… |
|---|---|
| **Year-based checklist (general + major)** | Giving a checklist for each year of school, with school-wide and major items |
| **Task and objective completion** | Letting the student check off work and see what is left |
| **Events and campus activities** | Connecting the checklist to real events they can do while in school |
| **Job-application readiness** | Focusing effort on being ready to apply successfully |
| **Student account and profile** | Saving class year, major, and progress to the student |
| **Publishing checklists and events** | Letting career advisors and faculty keep each year's plan current |
| **Progress for advisors, faculty, and teachers** | Letting staff help students stay ready to apply |
| **Administration** | Keeping accounts, roles, and the current year correct |
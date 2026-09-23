# Campus Parking — Open Product Questions

**Author:** Engineer (Intern / Junior)  
**Audience:** Product Owner / Mentor  
**Status:** Awaiting Product Owner decisions  
**Related:** [PRD.md](PRD.md), [BUSINESS_RULES.md](BUSINESS_RULES.md), [ACCEPTANCE_CRITERIA.md](ACCEPTANCE_CRITERIA.md)

---

## Purpose

This document lists product behaviors that are ambiguous or undefined in the current requirements.
Each question includes a **proposed default** so the Product Owner can approve quickly or choose another option.

The proposed defaults are suggestions only. Nothing here will be implemented until a decision is recorded.

## How to Read This Document

| Field | Meaning |
|---|---|
| **Priority** | `Blocker` = must be answered before the related RFC can be finalized. `Normal` = can be answered during implementation. |
| **Affects** | Business rules, acceptance criteria, and RFCs that depend on the answer. |
| **Proposed default** | The engineer's suggested behavior, with a short reason. |
| **Decision** | Filled in by the Product Owner. |

## Summary

| ID | Topic | Priority | Affects |
|---|---|---|---|
| Q-01 | Registration information | Normal | AC-A01, RFC-5 |
| Q-02 | How staff/admin accounts are created | Blocker | BR-013, RFC-5 |
| Q-03 | Disabled accounts | Normal | BR-004, RFC-5 |
| Q-04 | Role hierarchy | Blocker | BR-013, RFC-5 |
| Q-05 | License plate uniqueness | Blocker | BR-001, RFC-2 |
| Q-06 | Vehicle types and capacity | Blocker | BR-003, RFC-2 |
| Q-07 | Meaning of "remove vehicle" | Normal | BR-008, RFC-2 |
| Q-08 | One user, multiple active vehicles | Blocker | BR-002, RFC-2, RFC-3 |
| Q-09 | Parking area information | Normal | US-F01, RFC-2 |
| Q-10 | Capacity reduction below occupancy | Blocker | BR-011, AC-N03, RFC-3 |
| Q-11 | Parking area removal | Normal | BR-012, RFC-2 |
| Q-12 | Forgotten checkout | Normal | PRD §19, RFC-2 |
| Q-13 | Repeated checkout response | Blocker | BR-007, AC-J03, RFC-6 |
| Q-14 | Source of check-in / checkout time | Normal | BR-009, RFC-2 |
| Q-15 | Scope of staff correction | Blocker | BR-010, RFC-2 |
| Q-16 | Information required for manual close | Normal | BR-010, AC-M01, RFC-2 |
| Q-17 | Staff search criteria | Normal | AC-L01, RFC-6 |
| Q-18 | User data visible to staff | Normal | PRD §13, RFC-5 |
| Q-19 | Audit log scope and access | Normal | BR-014, AC-P01, RFC-2 |
| Q-20 | Availability freshness | Normal | PRD §12, RFC-4 |
| Q-21 | Staff/admin client platform | Blocker | RFC-1, RFC-7 |
| Q-22 | Application language | Normal | RFC-7 |
| Q-23 | Expected scale | Normal | RFC-1, RFC-4 |

---

## A. Identity and Roles

### Q-01 Registration information

- **Question:** What information is required to register? Must the email belong to a campus domain?
- **Priority:** Normal
- **Affects:** AC-A01, AC-A03, RFC-5
- **Options:**
  - A. Email + password + display name, any email domain.
  - B. Same as A, but only campus email domains are accepted.
  - C. Same as A, plus user type (student / lecturer / employee).
- **Proposed default:** A. Email verification is not in MVP scope, so a domain restriction gives limited protection without verification.
- **Decision:** _pending_

### Q-02 How staff and admin accounts are created

- **Question:** Role management is not listed in MVP scope. How does an account become staff or administrator?
- **Priority:** Blocker
- **Affects:** BR-013, US-A03, RFC-5
- **Options:**
  - A. The first administrator is created by a seed/setup script. Other staff/admin roles are assigned by direct database or script operation.
  - B. Add a small admin feature: an administrator can grant or revoke staff/admin roles (audited).
- **Proposed default:** A for MVP, because B adds a new feature outside the defined scope. B can be a future backlog item.
- **Decision:** _pending_

### Q-03 Disabled accounts

- **Question:** Does MVP need disabled accounts? If yes, can a disabled user still checkout an active session?
- **Priority:** Normal
- **Affects:** BR-004 (vehicles must be able to leave), RFC-5
- **Options:**
  - A. No disabled accounts in MVP.
  - B. Disabled accounts cannot log in at all; staff must close their active sessions.
  - C. Disabled accounts cannot start new parking but can still checkout.
- **Proposed default:** A, unless there is a known operational need. If disabling is required, C matches the spirit of BR-004 ("existing vehicles must still be able to leave").
- **Decision:** _pending_

### Q-04 Role hierarchy

- **Question:** Does an administrator also have all staff permissions (search sessions, manual close)?
- **Priority:** Blocker
- **Affects:** BR-013, AC-O01, RFC-5
- **Options:**
  - A. Hierarchical: user < staff < admin.
  - B. Separate: admin manages configuration only; staff handles sessions only.
- **Proposed default:** A. It is simpler to explain and test for MVP.
- **Decision:** _pending_

---

## B. Vehicles

### Q-05 License plate uniqueness

- **Question:** Is a license plate unique across the whole system? Should the province be part of the plate identity?
- **Priority:** Blocker
- **Affects:** BR-001, AC-C01, RFC-2 (database constraints)
- **Options:**
  - A. Plate number is unique system-wide.
  - B. Plate number + province is unique system-wide.
  - C. Unique only within one user account (two users may register the same plate).
- **Proposed default:** B, applied only to **active** vehicles. Thai plates can repeat across provinces, and a deactivated vehicle should not block a new owner from registering the same plate.
- **Decision:** _pending_

### Q-06 Vehicle types and capacity

- **Question:** Do vehicle types (car, motorcycle) matter? Does a parking area have separate capacity per type?
- **Priority:** Blocker
- **Affects:** BR-003, AC-D01, RFC-2, RFC-3
- **Options:**
  - A. No vehicle type in MVP. One capacity per area.
  - B. Vehicle type is recorded for information only. One capacity per area.
  - C. Each area accepts specific vehicle types, with one capacity per area.
  - D. Each area has a separate capacity per vehicle type.
- **Proposed default:** B. It keeps the capacity rule simple (PRD §7: "capacity is defined at parking-area level") while keeping the data for future use. D changes the concurrency design significantly, so it should be decided now if needed.
- **Decision:** _pending_

### Q-07 Meaning of "remove vehicle"

- **Question:** What does removing a vehicle mean to the user? Can a removed vehicle be restored?
- **Priority:** Normal
- **Affects:** BR-008, AC-C03, RFC-2
- **Options:**
  - A. Deactivate: hidden from choices, history kept, cannot be restored (user registers again).
  - B. Deactivate with a "restore" action.
- **Proposed default:** A. Restore is not requested and adds extra rules.
- **Decision:** _pending_

### Q-08 One user, multiple active vehicles

- **Question:** Can one user have two different vehicles parked at the same time?
- **Priority:** Blocker
- **Affects:** BR-002, RFC-2, RFC-3 (the uniqueness rule for active sessions)
- **Options:**
  - A. Yes. The limit is one active session per vehicle only (as written in BR-002).
  - B. No. One active session per user.
- **Proposed default:** A, because it matches BR-002 as written. Please confirm, because this changes the core database constraint.
- **Decision:** _pending_

---

## C. Parking Areas

### Q-09 Parking area information

- **Question:** What information must a parking area have?
- **Priority:** Normal
- **Affects:** US-F01, AC-D01, RFC-2
- **Proposed default:** Name (unique), short location description, capacity, operational status (open / closed / maintenance). No map coordinates or opening hours in MVP.
- **Decision:** _pending_

### Q-10 Capacity reduction below current occupancy

- **Question:** An administrator reduces capacity to a number lower than the current active sessions. What should happen?
- **Priority:** Blocker
- **Affects:** BR-011, AC-N03, RFC-3
- **Options:**
  - A. Reject the change. The administrator must wait or ask staff to close sessions first.
  - B. Accept the change. The area shows "full" (never negative availability) and rejects new parking until occupancy drops below the new capacity. Existing sessions continue normally.
  - C. Same as B, but only with an explicit override confirmation and a required reason (audited).
- **Proposed default:** C. Real-world capacity can drop suddenly (for example, part of the lot is closed for repairs), so rejecting may block a real operational need. The override and reason make the situation intentional and traceable, not silent.
- **Decision:** _pending_

### Q-11 Parking area removal

- **Question:** Can a parking area be deleted? What if it still has active sessions?
- **Priority:** Normal
- **Affects:** BR-012, RFC-2
- **Options:**
  - A. No deletion. Areas can only be archived. Archived areas are hidden from users but remain in history.
  - B. Soft delete with the same effect as A.
- **Proposed default:** A. Archiving is allowed only when the area has no active sessions.
- **Decision:** _pending_

---

## D. Parking Sessions

### Q-12 Forgotten checkout

- **Question:** What happens when a user forgets to checkout for hours or days?
- **Priority:** Normal
- **Affects:** PRD §19, BR-010, RFC-2
- **Options:**
  - A. Nothing automatic. Staff can find long sessions and close them manually.
  - B. The system automatically closes sessions older than N hours.
  - C. Same as A, plus staff search can filter "active longer than N hours".
- **Proposed default:** C. Automatic closing (B) may record wrong end times and changes occupancy without a human decision.
- **Decision:** _pending_ — if C, what is N?

### Q-13 Repeated checkout response

- **Question:** A user sends checkout for a session that is already completed (for example, a double tap or a network retry). What should the user see?
- **Priority:** Blocker
- **Affects:** BR-007, AC-J03, RFC-6
- **Options:**
  - A. Success, returning the already completed session (idempotent). Nothing changes.
  - B. An error such as "session already ended". Nothing changes.
- **Proposed default:** A. On mobile networks, the first request may succeed but the response may be lost. With A, the retry shows the correct final result instead of a confusing error. Capacity is never released twice in either option.
- **Decision:** _pending_

### Q-14 Source of check-in and checkout time

- **Question:** Are times always recorded by the server at the moment of the action, or can the user enter a time?
- **Priority:** Normal
- **Affects:** BR-009, RFC-2
- **Proposed default:** Server time only. User-entered times cannot be verified.
- **Decision:** _pending_

---

## E. Staff Operations

### Q-15 Scope of staff correction

- **Question:** Can staff only close active sessions, or also edit completed sessions (for example, fix a wrong end time)?
- **Priority:** Blocker
- **Affects:** BR-010, RFC-2 (history strategy)
- **Options:**
  - A. Close active sessions only. Completed history is never edited.
  - B. Also edit completed sessions, with reason and audit.
- **Proposed default:** A. It matches US-E02, and editing history makes the history strategy much more complex.
- **Decision:** _pending_

### Q-16 Information required for manual close

- **Question:** What must staff provide when closing a session? Which end time is recorded?
- **Priority:** Normal
- **Affects:** BR-010, AC-M01, AC-M02, RFC-2
- **Options:**
  - A. Free-text reason. End time = time of the staff action.
  - B. Reason category (for example: forgot checkout, wrong area, system issue) + free-text note. End time = time of the staff action.
- **Proposed default:** B. Categories make later reporting possible without changing the data.
- **Decision:** _pending_ — if B, please confirm the category list.

### Q-17 Staff search criteria

- **Question:** Which criteria must staff search support in MVP?
- **Priority:** Normal
- **Affects:** AC-L01, RFC-6
- **Proposed default:** License plate (partial match), user email, parking area, status (active / completed), date range, and "active longer than N hours" (see Q-12).
- **Decision:** _pending_

### Q-18 User data visible to staff

- **Question:** How much user information can staff see?
- **Priority:** Normal
- **Affects:** PRD §13, RFC-5
- **Proposed default:** Display name, email, and vehicles only. No other personal data is collected in MVP.
- **Decision:** _pending_

---

## F. Audit

### Q-19 Audit log scope and access

- **Question:** Which actions are audited, and who can view the audit log?
- **Priority:** Normal
- **Affects:** BR-014, AC-P01, RFC-2
- **Proposed default:**
  - Audited: parking area create/update/archive, capacity change, status change, staff manual close, capacity override (Q-10).
  - Not audited: normal user check-in/checkout (already recorded as parking history).
  - Viewers: administrators only.
- **Decision:** _pending_

---

## G. Availability and Experience

### Q-20 Availability freshness

- **Question:** How up to date must the availability shown in the list be? What should users see when it might be out of date?
- **Priority:** Normal
- **Affects:** PRD §12, RFC-4
- **Proposed default:** The list may be a few seconds behind. The app shows "updated at" time and supports pull-to-refresh. The check-in decision itself always uses authoritative data, so a stale list can never cause over-capacity.
- **Decision:** _pending_ — acceptable delay in seconds?

### Q-21 Staff and admin client platform

- **Question:** Do staff and administrators use the same Flutter mobile app, or a separate web interface?
- **Priority:** Blocker
- **Affects:** RFC-1, RFC-7
- **Options:**
  - A. One Flutter mobile app with screens based on role.
  - B. Flutter mobile app for users + Flutter web for staff/admin.
- **Proposed default:** A. One codebase and one deployment for MVP.
- **Decision:** _pending_

### Q-22 Application language

- **Question:** Which language(s) must the app support?
- **Priority:** Normal
- **Affects:** RFC-7
- **Options:** Thai only / English only / Thai and English.
- **Proposed default:** Thai only for MVP, with text kept in one place so English can be added later.
- **Decision:** _pending_

### Q-23 Expected scale

- **Question:** What are rough numbers for the pilot? (Number of parking areas, registered users, and peak check-ins, for example during the morning rush.)
- **Priority:** Normal
- **Affects:** RFC-1, RFC-4 (the engineer must propose performance targets, per PRD §4)
- **Proposed default:** If unknown, the design will assume about 20 areas, 5,000 users, and 500 check-ins in the busiest 30 minutes, and state these assumptions in the RFCs.
- **Decision:** _pending_

---

## Decision Log

Record decisions here once agreed, so later RFCs can reference them.

| ID | Decision | Decided by | Date |
|---|---|---|---|
| | | | |

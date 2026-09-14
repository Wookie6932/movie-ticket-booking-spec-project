# Movie Ticket Booking System - Specification

Owner: William | Individual assignment | Version: 0.1, initial draft | Date: September 14, 2026

Status: Working draft, not signed off. Participant feedback and the full business case have not been supplied. Numeric limits, timing and policies below are proposed individual-project decisions, not established team requirements.

Source basis: the copied `docs/assignment-2.md` summary and `docs/use-case-diagram.puml`, which identify accounts, browsing, booking, reservation management and theater administration. They do not contain the complete business case or detailed functional requirements. Original project attribution remains in `docs/design/README.md`.

## 0. Constitution

| # | Principle | Why it exists |
|---|---|---|
| 1 | Never sell or confirm the same seat twice for one showtime. | A reservation must be trustworthy. |
| 2 | Collect no real card information in the assignment prototype. | This is a classroom simulation. |
| 3 | Show the total and cancellation rules before confirmation. | Customers need an informed decision. |
| 4 | Protect customer reservations and restrict administrative changes. | Account ownership and theater operations must be respected. |
| 5 | Make the core flow usable without a mouse or color perception. | Essential actions must be accessible. |

## 1. Problem & Intent

**Who is this for?** Moviegoers booking one to six seats at one configured theater, and the theater administrator maintaining its schedule. A student audience is a recruitment convenience for this assignment, not a validated market segment.

**What problem do they have today?** Working hypothesis: comparing showtimes, finding seats together and understanding the final price and cancellation options can require too much effort. Interviews must verify how prospective users actually book and which problems they experience.

**Why now / why us?** The existing team concept provides five coherent use cases for a manageable individual design exercise. This specification develops that concept independently; it does not claim a proven competitive advantage.

**What does success look like?** Proposed usability target: at least 80% of five future representative participants can reserve two seats and identify their reference without assistance in three minutes, with zero wrong-showtime confirmations. The assignment's minimum two external observations are formative and cannot establish this success rate. Proposed correctness target: zero duplicate seat allocations in a 20-request concurrent booking test. No target has yet been measured.

## 2. Scope

**In scope:** one theater and its configured auditoriums; customer registration/sign-in, display-name updates and sign-out; movie/date/showtime browsing; seat selection; held seats and simulated confirmation; viewing and cancelling an entire reservation; administrator movie/showtime management; explicit loading, empty, expired, conflict and failure states.

**Out of scope:** live payments and refunds, actual tickets usable at a cinema, external theater inventory, guest checkout, loyalty schemes, concessions, discounts, partial cancellation, seat exchanges, native apps and multi-theater search. Email verification/reset and administrator cancellation of booked screenings require later decisions before production use.

The paper prototype evaluates the customer booking/cancellation flow and a sample administrator save. It cannot implement authentication, storage, concurrency or production accessibility; those remain acceptance requirements for a later working app.

## 3. User Scenarios

### Scenario 1: Find a suitable screening
- Actor: prospective customer.
- Trigger: wants a movie outing on a chosen date.
- Steps: choose date; compare movie details and published future showtimes; choose one.
- Success outcome: the chosen title, date, theater and local start time remain visible during booking.
- Failure outcome: no matches or sold-out screening produces an explanation and another-date/showtime option.

### Scenario 2: Create and manage an account
- Actor: visitor or returning customer.
- Trigger: needs to confirm a reservation or access previous bookings.
- Steps: register or sign in; correct any field errors; update display name if desired; sign out when finished.
- Success outcome: authenticated access is limited to the account's reservations; chosen movie/showtime survives sign-in.
- Failure outcome: invalid input is explained; failed authentication never opens another customer's data.

### Scenario 3: Reserve adjacent seats
- Actor: signed-in customer.
- Trigger: chooses a screening and wants two seats together.
- Steps: inspect map; select two adjacent available seats; continue to acquire a hold; review details and total; confirm; record the reference.
- Success outcome: one reservation owns both seats, and the reference is available in My reservations.
- Failure outcome: a conflict or expired hold returns to selection; unknown confirmation status offers a status check instead of creating duplicates.

### Scenario 4: Cancel a reservation
- Actor: reservation owner.
- Trigger: plans change before the screening starts.
- Steps: open My reservations; choose Cancel; check affected screening/seats and policy; confirm or keep reservation.
- Success outcome: confirmed cancellation changes status and releases seats once.
- Failure outcome: at/after showtime cancellation is refused; an unsuccessful request leaves a clear status and safe retry.

### Scenario 5: Publish a screening
- Actor: theater administrator.
- Trigger: theater needs to add a movie and showtime.
- Steps: sign in as administrator; enter movie metadata; enter auditorium, date/time, format and price; validate; publish.
- Success outcome: valid screening is available on its scheduled date.
- Failure outcome: invalid fields, auditorium overlap or changes to a booked screening are rejected without losing valid entries.

## 4. Requirements (EARS notation)

| ID | Requirement | Pattern |
|---|---|---|
| R1 | When a visitor chooses a date, the system shall list only published future showtimes at the configured theater, with title, rating, runtime, format and local start time. | Event |
| R2 | If no showtimes match the chosen date, then the system shall show an empty-state explanation and a control to choose another date. | Unwanted |
| R3 | When a visitor registers with a valid unused email and a password of at least 15 characters, the system shall create a customer account; when credentials are valid, the system shall sign the customer in. | Event |
| R4 | When a signed-in customer changes their display name or signs out, the system shall save the name or end the authenticated session respectively. | Event |
| R5 | When a customer chooses a showtime, the system shall show its seat map, screen orientation, seat IDs and available, selected and unavailable states. | Event |
| R6 | When a customer selects or deselects available seats, the system shall update the seat list, ticket count and total; if the count is outside 1 to 6, then the system shall prevent continuation. | Event / Unwanted |
| R7 | When a signed-in customer continues from seat selection, the system shall atomically hold all chosen seats for five minutes; if any seat is no longer available, then it shall hold none and explain which seats must be reselected. | Event / Unwanted |
| R8 | While a hold is active, the system shall display its remaining time; if it expires, then it shall release the seats, prevent confirmation and return the customer to seat selection with an explanation. | State / Unwanted |
| R9 | When a customer reviews a reservation, the system shall show movie, theater, date, local time, format, seats, itemized ticket subtotal, fees, taxes, total and cancellation cutoff before confirmation. | Event |
| R10 | When a customer confirms a valid unexpired held reservation, the system shall create one confirmed reservation and one unique reference and change its seats to booked; repeated confirmation of the same request shall return the same reservation. | Event |
| R11 | If confirmation fails or its result is unknown, then the system shall show the known status and offer a safe status check; it shall not tell the customer to create a second booking while the first result is unknown. | Unwanted |
| R12 | When a reservation is confirmed, the system shall show its reference, booking details, status and a link to My reservations. | Event |
| R13 | When an authenticated customer opens My reservations, the system shall show only that customer's reservations with their current confirmed or cancelled status. | Event |
| R14 | When a customer requests cancellation before the showtime, the system shall display the affected reservation and require an explicit confirmation; after confirmation it shall mark the entire reservation cancelled and release its seats exactly once. | Event |
| R15 | If a showtime has started, then the system shall reject cancellation and show the cutoff reason in the theater's local time. | Unwanted |
| R16 | When an authorized theater administrator saves a valid movie or future showtime, the system shall persist it and show published entries to customers. | Event |
| R17 | If a showtime overlaps another in the same auditorium, has invalid fields, or an edit would change the time or auditorium of a showtime with active reservations, then the system shall reject the save and explain the conflict. | Unwanted |
| R18 | The system shall restrict movie and showtime changes to theater administrators using server-side authorization. | Ubiquitous |
| R19 | The system shall support keyboard use of the booking flow, visible focus, labeled fields and errors, and seat states conveyed without relying on color alone. | Ubiquitous |
| R20 | The assignment prototype shall display a simulation notice and shall neither collect payment card details nor charge money. | Ubiquitous |

## 5. Acceptance Criteria

These are planned pass/fail checks, not claims of completed implementation testing.

| Requirement | Test | Pass condition |
|---|---|---|
| R1 | Choose a date with two future and one past showtime. | Exactly the two future published showtimes appear; all six details are visible. |
| R2 | Choose a date with no showtimes. | No stale results appear; the date can be changed. |
| R3 | Register, sign out and sign in; repeat with duplicate email, malformed email and short password. | Valid case succeeds; each invalid case explains the affected field without creating another account. |
| R4 | Change a name, reload, then sign out and request reservations. | Name persists; reservation access requires sign-in after sign-out. |
| R5 | Open a showtime with unavailable seats. | Seat IDs and all states are distinguishable by text or symbols as well as color; unavailable seats cannot be selected. |
| R6 | Select two seats, deselect one, attempt zero and seven. | Counts and totals agree; only 1 to 6 seats can proceed. |
| R7 | Two sessions request the same seats concurrently. | Only one session obtains the conflicting hold; the other gets an actionable conflict message and no partial hold. |
| R8 | Expire the hold before confirmation. | Seats become available again; confirmation fails; movie and showtime remain selected. |
| R9 | Review a two-ticket fixture at $12 each, $0 fee and $0 tax. | All details appear; the total is $24.00; editing seats updates the total before confirmation. |
| R10 | Confirm twice using the same request identifier. | Exactly one reservation/reference exists and its seat count equals the requested count. |
| R11 | Simulate server failure and a lost confirmation response. | Known failure is distinguished from pending/unknown; checking the request returns its existing result without duplication. |
| R12 | Complete one valid reservation. | Confirmation and the customer reservation list show the same reference, showtime and seats. |
| R13 | Use two accounts and attempt to open the other account's reservation directly. | Each sees only their own data; unauthorized direct access is rejected. |
| R14 | Preview cancellation, keep reservation, then confirm cancellation twice. | Preview alone changes nothing; first confirmation cancels; repeated confirmation creates no additional effect. |
| R15 | Attempt cancellation one second before, at and after the start time. | Before start is permitted; at/after start is refused; the reason is visible. |
| R16 | Save a movie with title, rating and positive runtime, then a future showtime with auditorium, format and nonnegative price. | Saved details persist and published showtime appears on the correct customer date. |
| R17 | Try overlapping screenings, past time, negative price and moving a booked screening. | All invalid edits fail with useful messages; valid non-overlapping entries remain unchanged. |
| R18 | Send an administrator save request as a visitor and as a customer. | Both are denied and no stored data changes. |
| R19 | Complete browse, select, review and cancellation with keyboard only; inspect accessible names and contrast. | No keyboard trap; every action has visible focus and an understandable name; normal text meets 4.5:1 contrast. |
| R20 | Inspect every prototype screen and complete the simulated flow. | Simulation is identified; no real payment field, payment request or charge is present. |

## 6. Constraints & Non-Functional Requirements

- **Performance:** proposed deployed-app target: 95% of browse/seat reads finish within two seconds at 20 concurrent users on the documented test environment. Test before claiming compliance; the paper prototype cannot measure this.
- **Security/Privacy:** production credentials require a maintained authentication library, password hashing, HTTPS and server-side ownership/role checks. Collect only email, password credentials, display name and reservation data. No real personal data in public fixtures or research notes; use P1/P2 aliases. Retention period is unresolved.
- **Accessibility:** target WCAG 2.2 AA. Keyboard access, text seat labels, visible focus, contrast and readable field errors are required. A future implementation needs a full accessibility review; a paper prototype cannot demonstrate conformance.
- **Compliance/Legal:** use fictional movie fixtures or licensed content. Live commercial ticketing, payment handling, refund obligations and deployment privacy notices are outside this classroom prototype and need separate review before launch.
- **Budget/Timeline:** no paid services authorized. Proposed sequence: draft, desk research, paper prototype, two external evaluations, then final revision and PDF. Assignment deadline is unknown.
- **Business rules:** one theater; theater timezone America/Indiana/Indianapolis; store unambiguous timestamps and show local date/time. Five-minute server-timed holds; one to six tickets; whole-reservation cancellation only before showtime. Overlap includes runtime plus a proposed 15-minute turnaround. Sample price is $12, with $0 sample fees/tax, solely for testing and not an actual tax determination.
- **State definitions:** a seat is available, held until a timestamp, or booked for a showtime. A reservation is confirmed or cancelled. Pending/unknown describes an in-flight request, never a second reservation. Holds are rechecked server-side when confirming. All selected seats succeed or fail together. Editing seats relinquishes the previous hold and obtains a new hold only on Continue; explain that availability may change.

## 7. Open Questions

| Question | Owner | Status |
|---|---|---|
| Does the full business case support the audience and one-theater scope? Supply and reconcile it. | William | Open; current repository has only a summary. |
| Do customers actually experience the hypothesized pains? | William / participants | Open; interviews pending. |
| Are five-minute holds, six-ticket limit, cancellation cutoff and turnaround acceptable? | William / theater stakeholder | Open; proposed prototype rules only. |
| Are email verification, password recovery, accessible-seat eligibility and guest checkout needed? | William | Open; not silently assumed implemented. |
| What happens when the theater cancels a booked screening? | Theater stakeholder | Open; blocked administrator edits avoid inventing policy. |
| What are the final deadline, hosting budget and data retention policy? | William | Open. |
| What changes follow the two external usability observations? | William / P1 / P2 | Open; no observations recorded yet. |

## 8. Plan

See `plan.md` and `tasks.md`. Review the requirements and plan before expanding into a working application. The authorized classroom prototype is a paper flow using fictional data; it does not approve production implementation or commercial launch.

## 9. Approval

| Role | Name | Date | Signed off? |
|---|---|---|---|
| Spec owner | William | Pending | No; needs review and participant findings. |
| Reviewer | To be named | Pending | No. |

# Research and evaluation working notes

September 14, 2026. Evidence status: desk research complete for the scope below; primary participant research pending. Sources were reviewed as public documentation/search-indexed official text. No competitor purchase was made and no timed competitor usability test was performed. Some live pages blocked automated access; these findings concern documented flows, not independently verified screen-by-screen checkout behavior.

## Competitive analysis

### AMC Theatres
Published seat-selection pages identify the theater and reserved seating, with a sold-out message directing people to choose another showtime. Its refund FAQ directs customers to account tickets or confirmation email; it explains that purchases cannot simply be edited and that ordinary eligible refunds must occur before showtime, excluding convenience fees. Implication: preserve screening context, provide a sold-out recovery path, and explain cancellation before confirmation. Do not copy AMC's commercial policy as if it were this project's policy. [AMC seat selection](https://www.amctheatres.com/showtimes/140658820/seats); [AMC refunds](https://www.amctheatres.com/faqs/refunds).

### Fandango
Fandango's publisher-provided app description advertises theater discovery, seat-map previews, reserved seating and mobile tickets. Its ticket policy describes refund/exchange initiation through an account or confirmation email, with eligibility restrictions and different treatment of convenience fees and credit. Documented flow: find a theater/showing, inspect seats, buy tickets, retrieve a mobile ticket and use account/email for after-purchase management. Opportunity: keep retrieval and cancellation easy to locate without adding loyalty or credit complexity. [Official app listing](https://play.google.com/store/apps/details?id=com.fandango); [Ticket policy](https://www.fandango.com/policies/ticket-and-concessions-policy).

### Atom Tickets
Atom's help documentation describes selecting a movie/showtime, selecting seats and proceeding to checkout; it offers ticket retrieval through Manage Ticket or an account. Its purchase terms distinguish a refund excluding convenience fees from account credit including fees, subject to conditions. Opportunity: make the selected seat list and final total persistent, and show exactly what cancellation changes. Concessions and account-credit schemes are unnecessary for this first version. [Atom support](https://www.atomtickets.com/help/support); [How Atom works](https://www.atomtickets.com/help/entry/how-does-atom-work); [Purchase terms](https://www.atomtickets.com/purchase-terms).

**Cross-competitor interpretation:** browsing, seat selection, confirmation retrieval and after-purchase management are recurring documented functions. This supports the existing use-case scope, but does not prove demand or ease of use. The team's business case and actual participant responses are still needed. Price transparency and explicit cancellation are design hypotheses to test, not quoted user complaints.

## User interviews / survey

Recruit at least two people who have booked cinema tickets; the assignment specifies three or more questions but does not specify an interview sample size. Use P1 and P2 aliases. Ask before showing the design:
1. Think about the last time you bought movie tickets. How did you choose the theater, time and seats, and complete the booking?
2. Which part was confusing, slow or frustrating? Describe a specific example, if any.
3. What would you most like a booking service to make easier or clearer, and why?
4. How do you find a ticket later or change plans? What information do you need before cancelling?

No responses have been collected. Do not convert these questions or desk-review hypotheses into participant findings.

## Technical feasibility

**Seat integrity:** PostgreSQL documents conflicting row locks and transaction boundaries. Proposed implementation: one seat-inventory row per showtime/seat, transactionally claim all chosen rows in a stable order, persist hold expiry, then recheck ownership/expiry when booking. A five-minute hold is stored data; do not hold a database transaction open while a customer thinks. Verify with concurrent requests and idempotent confirmation tests. This is a feasible design approach, not implemented evidence. [PostgreSQL explicit locking](https://www.postgresql.org/docs/current/explicit-locking.html).

**Payments:** Stripe Checkout documents hosted checkout options. If real payments are later approved, a provider-hosted flow is an option; completed payment still needs reliable server confirmation and inventory reconciliation. Payment integration and refunds are excluded now, so the paper prototype collects no card data. [Stripe Checkout](https://docs.stripe.com/payments/checkout).

**Accessibility:** W3C's WCAG 2.2 reference supports checks for keyboard operation, focus, labels, contrast and not conveying information by color alone. A small seat map can use labeled buttons in an eventual app, with a text seat list. A visual prototype alone cannot demonstrate conformance. [WCAG 2.2 reference](https://www.w3.org/WAI/WCAG22/quickref/).

**Hosting:** GitHub Pages can publish the specification and static prototype materials. It is not the server/database needed for authentication, authoritative inventory or production reservations. This assignment uses a paper prototype, so that limitation does not prevent interaction evaluation. [GitHub Pages overview](https://docs.github.com/en/pages/getting-started-with-github-pages/what-is-github-pages).

## Participant evaluation protocol

Use the prototype PDF on screen or print it. These are paper screens: participants point to controls and explain their next action; the facilitator changes pages and records choices. All movies, prices, account details and references are fictional. Do not request passwords, payment details or identifying personal data.

Task A: You and a friend want to see The Last Orbit at 7:15 PM on September 18. Find two adjacent available seats, tell me the total and confirm the simulated booking. Where would you find the reference later?

Task B: Your plans changed before showtime. Find the reservation and cancel it. Tell me what happens to the seats. First show the cancellation preview and ask whether the booking is already cancelled.

Task C: Your selected seats became unavailable before Continue, or your hold expired. Explain what you would do next using the recovery panel.

Do not teach the controls before the first attempt. Capture elapsed time only as a paper-task observation, not as application performance. Record at least two external observations, one from each person. William should add his own evaluation separately from the desk review supplied with this package.

| Participant | Task | What they did / exact words | Difficulty / outcome | Suggested change | Date |
|---|---|---|---|---|---|
| P1 | Pending | No observation collected | Pending | Pending | Pending |
| P2 | Pending | No observation collected | Pending | Pending | Pending |
| William | Pending | Owner evaluation not yet supplied | Pending | Pending | Pending |

## Completion gate

Before submission: add real interview responses and two external prototype observations; summarize themes without inflating the sample; revise all specification sections; verify the exact Pages URL; export the updated PDF. The current report is a draft, not a completed research claim.


## Prototype inspection and provisional revisions

Five rendered paper screens were inspected on September 14, 2026. This was a desk walkthrough, not a participant session and not William's personal evaluation. The seat map differentiates OPEN/PICK/X states; the review shows a $24 sample total; cancellation has a keep-versus-confirm decision.

Three observations: selection must be distinguished from a held seat (R6); editing seats must explain the release of a hold (R9); the cancellation panel must say preview/not yet cancelled (R14). These requirements and matching acceptance criteria were revised. Further review also blocks movie/runtime changes on booked screenings (R17). Every section's disposition is recorded in specification.md.

Paper-test limitations: highlighted sample seats and facilitator instructions can lead participants. Cover helper notes, ask participants to choose seats before revealing the worked B3/B4 example, and record when a facilitator supplied information. Runtime behavior, authentication, concurrency and keyboard/screen-reader behavior are not implemented or validated by these sheets.

Recruitment update: William selected two readily available family participants with different reported comfort levels using technology. They will be reported as P1/P2. No responses have yet been received. This convenience sample offers formative feedback and does not represent all cinema customers.

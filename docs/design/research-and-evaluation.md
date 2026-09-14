# Research and evaluation working notes

September 14, 2026. Evidence status: desk research complete for the scope below; participant summary received from William. Sources were reviewed as public documentation/search-indexed official text. No competitor purchase was made and no timed competitor usability test was performed. Some live pages blocked automated access; these findings concern documented flows, not independently verified screen-by-screen checkout behavior.

## Competitive analysis

### AMC Theatres
Published seat-selection pages identify the theater and reserved seating, with a sold-out message directing people to choose another showtime. Its refund FAQ directs customers to account tickets or confirmation email; it explains that purchases cannot simply be edited and that ordinary eligible refunds must occur before showtime, excluding convenience fees. Implication: preserve screening context, provide a sold-out recovery path, and explain cancellation before confirmation. Do not copy AMC's commercial policy as if it were this project's policy. [AMC seat selection](https://www.amctheatres.com/showtimes/140658820/seats); [AMC refunds](https://www.amctheatres.com/faqs/refunds).

### Fandango
Fandango's publisher-provided app description advertises theater discovery, seat-map previews, reserved seating and mobile tickets. Its ticket policy describes refund/exchange initiation through an account or confirmation email, with eligibility restrictions and different treatment of convenience fees and credit. Documented flow: find a theater/showing, inspect seats, buy tickets, retrieve a mobile ticket and use account/email for after-purchase management. Opportunity: keep retrieval and cancellation easy to locate without adding loyalty or credit complexity. [Official app listing](https://play.google.com/store/apps/details?id=com.fandango); [Ticket policy](https://www.fandango.com/policies/ticket-and-concessions-policy).

### Atom Tickets
Atom's help documentation describes selecting a movie/showtime, selecting seats and proceeding to checkout; it offers ticket retrieval through Manage Ticket or an account. Its purchase terms distinguish a refund excluding convenience fees from account credit including fees, subject to conditions. Opportunity: make the selected seat list and final total persistent, and show exactly what cancellation changes. Concessions and account-credit schemes are unnecessary for this first version. [Atom support](https://www.atomtickets.com/help/support); [How Atom works](https://www.atomtickets.com/help/entry/how-does-atom-work); [Purchase terms](https://www.atomtickets.com/purchase-terms).

**Cross-competitor interpretation:** browsing, seat selection, confirmation retrieval and after-purchase management are recurring documented functions. This supports the existing use-case scope, but does not prove demand or ease of use. The team's business case and actual participant responses are still needed. Price transparency and explicit cancellation are design hypotheses to test, not quoted user complaints.

## User interviews and prototype evaluation

William supplied interview responses from two participants after sharing the mockups. See [participant-feedback.md](participant-feedback.md) for the responses in their supplied wording, followed by findings and design decisions. No task-performance measurements were supplied.

P1 reported reliance on help/box-office purchasing, unexpected fees, uncertain seat-map orientation and pressure from the five-minute hold. P2 reported a preference for short text, strong visual cues and recovery that preserves the screening context. Both supported deliberate cancellation confirmation; their views on six-ticket limits differed.

These findings revise R6, R8, R14 and R19. The interviews asked about current purchasing, pain points, the hold duration, group size, cancellation and desired improvements.

## Technical feasibility

**Seat integrity:** PostgreSQL documents conflicting row locks and transaction boundaries. Proposed implementation: one seat-inventory row per showtime/seat, transactionally claim all chosen rows in a stable order, persist hold expiry, then recheck ownership/expiry when booking. A five-minute hold is stored data; do not hold a database transaction open while a customer thinks. Verify with concurrent requests and idempotent confirmation tests. This is a feasible design approach, not implemented evidence. [PostgreSQL explicit locking](https://www.postgresql.org/docs/current/explicit-locking.html).

**Payments:** Stripe Checkout documents hosted checkout options. If real payments are later approved, a provider-hosted flow is an option; completed payment still needs reliable server confirmation and inventory reconciliation. Payment integration and refunds are excluded now, so the paper prototype collects no card data. [Stripe Checkout](https://docs.stripe.com/payments/checkout).

**Accessibility:** W3C's WCAG 2.2 reference supports checks for keyboard operation, focus, labels, contrast and not conveying information by color alone. A small seat map can use labeled buttons in an eventual app, with a text seat list. A visual prototype alone cannot demonstrate conformance. [WCAG 2.2 reference](https://www.w3.org/WAI/WCAG22/quickref/).

**Hosting:** GitHub Pages can publish the specification and static prototype materials. It is not the server/database needed for authentication, authoritative inventory or production reservations. This assignment uses a paper prototype, so that limitation does not prevent interaction evaluation. [GitHub Pages overview](https://docs.github.com/en/pages/getting-started-with-github-pages/what-is-github-pages).

## Evaluation and next steps

Initial desk inspection identified selection-versus-hold ambiguity, missing edit-hold warnings and cancellation-preview ambiguity. The new participant review supports additional timing and visual improvements. The revised prototype shows early price itemization, an extension option and differentiated cancellation controls. Participants have not retested it.

All specification sections were reviewed; see the v0.3 revision table. Before final submission, William should review the report, add his own evaluation, reconcile the full business case and check the exact Markdown URL in a regular browser. The HTML companion is browser-readable. No production performance, transactional integrity or accessibility conformance test has been completed.

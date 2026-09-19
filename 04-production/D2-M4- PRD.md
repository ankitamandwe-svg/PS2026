# Living PRD

> Module 4 · Production Specs. Refactor for readability; extract a living PRD that stays true as the build evolves.

## Problem

_What user problem does this solve? Tie to the validated hypothesis._

New therapists on Danbi have no reviews, and buyers read the absence of reviews as risk. The prototype states the gap directly: 41% of active provider profiles have 0 reviews, zero-review providers book at 2.3% vs 14% for reviewed providers, and 68% of sessions go search → profile → exit without booking. This is a cold-start trap — a provider cannot earn reviews without first being booked.
Two earlier tests in the prototype's experiment log make this the validated hypothesis rather than a guess:
Insurance price display (Jul 14 – Aug 1, 2026): profile views +6%, first-booking rate flat (2.3% → 2.4%). Price clarity gets buyers onto profiles; the drop-off is at trust, not cost.
Same-week availability badge (Jun 2 – Jun 27, 2026): +1.9 pts for reviewed providers, no lift for zero-review providers. Availability only helps providers who already have credibility.
Shorter intake form (killed, May 2026): buyers abandon before starting, not because of paperwork.
Hypothesis under test: verified trust checks (identity, license, background, responsiveness) can substitute for a missing track record and lift first bookings for zero-review providers. Kill switch already written into the prototype: if verified badges do not lift first bookings, trust is not the barrier — pivot to affordability, fit, availability, or first-session risk rather than tuning badge copy or color.

## Users & jobs

- **Primary user:** Primary user: a prospective therapy client (buyer) browsing for a first appointment. Secondary user: a new provider with 0 reviews. Third user: the product team running the test.
- **Job to be done:** Primary user: "Help me pick a therapist I can trust enough to book a first session, when I have nothing to go on." Secondary user: "I'm great at my job but I'll never get a review if no one books me first." Third user: Show me whether the trust panel moves first bookings, and let me record continue-or-pivot

## Scope

- **In:** Search results with Verified badge, trust-check meter, and track-record meter per provider.
Provider detail page with a sticky Trust Checks panel (identity confirmed, license active, background clear, reliable response) and an explicit empty state for zero-review providers.
Booking dialog: slot pick → confirmation, with event tracking.
Control vs Trust-panel variant comparison on the experiment readout page.
Experiment hub listing past tests, decisions, and learnings; continue/pivot decision capture.
Sign-in / sign-up (email, Google, Apple) plus guest browsing.
Supporting screens: payments summary, client profile, provider activity dashboard.
- **Out (explicitly):** Real verification vendors (ID, license registry, background checks).
Real payment capture or insurance eligibility.
Review collection, messaging, video sessions, provider onboarding.
Randomized variant assignment and statistical significance testing.
Server-side experiment analysis / dashboards over real event data.

## Requirements

| # | Requirement | Priority | Acceptance criteria |
|---|---|---|---|
| 1 | Trust panel on provider profile | Must | Zero-review provider profile shows four verification items with supporting notes and a 100% trust-check meter; panel stays visible while scrolling on desktop |
| 2 | Trust signals in search results | Should | Every result row shows Verified badge, trust-check %, track-record %, next availability, and price; zero-review rows link to "See trust checks" instead of "Read N reviews |

## Data & events

_What gets stored, what gets tracked._

Real (backed by the database):
profiles — id, display_name, timestamps; row-level security restricted to the owner; auto-created on signup.
experiment_events — user_id, variant (control | trust), event_name (search_impression, profile_view, trust_view, booking_start, booking_confirmed), therapist_id, created_at. Owner-only read and insert.
Supabase auth: email/password, Google, Apple.
Mocked / client-only:
All three therapists, their photos, rates, availability, review quotes, trust %, and track-record % are hard-coded in the source.
Every trust check (identity, license, background, response time) is static copy — no verification service is called.
Confirmed bookings, chosen role, client profile fields, and the continue/pivot decision are stored in browser local storage only.
Readout funnel numbers (control 1,240/476/11; trust 1,240/512/16) are editable defaults, not measured results. The historical experiments on the hub are fabricated records.
Payments (card ending 4242, coverage active, $0.00 balance) and provider dashboard activity are display-only.
Every visitor sees the trust variant; there is no randomized assignment, and no analysis reads experiment_events back.
Events needed before a real test: randomized assignment persisted per user, control events actually emitted, event capture for guests, and an aggregate read of the funnel per arm.

## Open questions

What lift over the 2.3% zero-review booking rate counts as success, and at what sample size do we call it?
How is assignment randomized and held stable — per user, per session, or per provider profile?
Which verifications are real at launch, and who supplies them (ID, license registry, background, response-time measurement)?
Is the trust panel shown for all providers or only zero-review ones? Showing it everywhere may dilute the signal for reviewed providers.
What is the legal and compliance position on displaying "Background clear" and license status for licensed clinicians?
Does a false or stale verification create liability, and how quickly must checks be re-run?
Should guest (not signed-in) traffic be included in the experiment, given events currently require a signed-in user?
Are the trust-check and track-record percentages computed from something real, or are they a design device to be removed?

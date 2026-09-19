# Engineering Handoff Note

> Module 4 · Production Specs. Open the black box, make the build legible to an engineer.

## What this is

_One paragraph an engineer can read in 60 seconds._

Danbi Mind is a high-fidelity, clickable marketplace prototype testing whether prominent verification and ethical early social proof improve first-booking rates for therapists with zero reviews. It is a TanStack Start/React 19 application organized by product feature, with thin route files and screen, data, and state modules grouped under each feature. The primary journey starts at sign-in, allows a guest bypass, continues through Find Care, opens a therapist profile, and confirms a time window; My Profile, Payments, provider-only Activity, and an interactive Experiments Hub complete the prototype. Lovable Cloud supplies authentication, user profiles, and authenticated experiment-event storage, while therapist records, experiment results, provider activity, payments, bookings, role selection, and experiment decisions are currently seeded or browser-local. Treat this as a validated interaction prototype and engineering starting point—not as production booking, payment, verification, authorization, or experimentation infrastructure.

## Architecture (plain language)

- **Frontend:** Framework: TanStack Start v1, React 19, TypeScript, Vite, and Tailwind CSS v4. Routing: Route files in src/routes/ define URLs, metadata, and legacy redirects. They render named screens from src/features/. Feature organization: Each product area owns its screen and supporting data/model modules: auth — sign-in, validation, role choice, and guest continuation find-care — searchable provider results therapists — provider data and profile/trust presentation booking — booking window dialog and local booking model profile — editable client profile and local profile model payments — payment summary linked to the locally confirmed booking provider-activity — provider metrics and role-gated display experiments — hub, historical readouts, live simulator, decision model, and event tracking Shared presentation: src/components/ contains the app header, brand, auth gate, and reusable UI primitives. Design system: Semantic tokens live in src/styles.css; the visual language is sage green with Newsreader and Manrope. Canonical URLs: /signin, /find-care, /therapists/:therapistId, /my-profile, /payments, /provider-activity, /experiments-hub, /experiments-hub/live, and /experiments-hub/:experimentId. Compatibility URLs: Earlier paths such as /auth, /profile, /provider, /experiment, and /experiments redirect to their canonical equivalents.
- **Backend / data:** Lovable Cloud auth: Email/password and social sign-in are wired through the generated cloud client. Guest entry remains available for prototype review. Cloud tables: profiles stores an authenticated user's display name. experiment_events accepts authenticated funnel events: search impression, profile view, trust view, booking start, and booking confirmed. Access controls: Both tables use row-level access rules so authenticated users can only read or write their own records. Profile rows are created automatically after account creation. Browser-local state: A single confirmed booking, profile edits, selected role, guest name, deep-link destination, and live experiment decision are kept in localStorage or sessionStorage. Seeded data: Therapist listings, reviews, availability, historical experiment outcomes, live experiment starting values, and provider activity metrics are TypeScript fixtures—not cloud records. Tracking limitation: Experiment events are recorded only for authenticated users. Guest activity is intentionally not persisted.
- **Key flows:** Entry and deep linking

/ redirects to /signin?next=/find-care.
A user may sign in with validated credentials, choose social sign-in, or skip as a guest.
The intended destination is preserved and restored after authentication.
Find care to booking

Find Care filters seeded therapists by name, credentials, specialties, and experience.
“Book now” in a search row opens directly at the time-window picker.
Opening the profile exposes credentials, verification checks, responsiveness signals, and reviews where available.
Dr. Amara Bennett deliberately has zero reviews so the core trust hypothesis is observable.
Confirmation stores one booking locally and updates Payments and Provider Activity in the same browser.
Profile and payments

My Profile reads and writes browser-local profile details.
Payments displays the current Find Care booking; without one, it links back to Find Care.
No money moves and no payment processor is connected.
Provider activity

Selecting the provider role reveals the Provider navigation item and Activity screen.
Selecting the client role shows an access-gated message instead.
Seeded utilization, confirmations, cancellations, and no-shows are adjusted when the local booking matches a provider.
Experiment operation

The hub shows one live experiment and seeded historical experiments.
The live screen compares control and trust-panel presentations, accepts what-if funnel inputs, recalculates conversion, keeps the kill rule visible, and records a manual Continue/Pivot decision locally.
The cloud event table captures authenticated interaction events, but the readout does not aggregate those events; displayed experiment numbers remain simulated.

## What's solid vs. what's duct tape

| Area | State | Notes |
|---|---|---|
| Feature-based code structure and PRD-aligned screen names are established. | solid | _____ |
| Authentication is optional by design, and the guest identity is just a browser value. | rough | _____ |

## Risks & assumptions for the team

Success threshold is unresolved: The required lift, minimum sample size, experiment duration, and confidence standard need explicit agreement.
Biased measurement: Excluding guest traffic may materially distort the zero-review first-booking rate.
No assignment integrity: Without server-side assignment and exposure logging, control/trust comparisons cannot support a causal conclusion.
Metric definition: “First booking,” eligible buyer, zero-review status, cancellations, and duplicate attempts need durable definitions before implementation.
Small-sample behavior: A few bookings can create a large apparent lift for new providers; guardrails and uncertainty must be shown.

## How to run it

```
Open the local URL printed by Vite. The normal starting path is /, which redirects to sign-in. Choose Skip for now for the quickest review path.
```

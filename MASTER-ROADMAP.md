# alcoia — master roadmap (billing, console, accounts)

**This file replaces `BILLING-ROADMAP.md`. Use this one.**

**Read this whole introduction before doing anything.** It answers "am I lost" directly.

---

## The one-paragraph map

You have three repositories. `alcoiaServer` is done through S11 (Creem is wired). `alcoia` (the
extension) has zero of this — no sign-in, no billing UI wired, no way to join a class. A fourth
repo, the admin console, **does not exist yet** — you have never created it. This document takes
you from here to all three being wired together, in an order where each step is small and nothing
requires you to spend money or own a domain until the very last section.

**Where each phase happens:**

| Phase | Repo |
|---|---|
| 0 | Nowhere — just your local machine, ngrok, and a Creem test account |
| 1 | `alcoia-group/alcoiaWeb` (marketing site) |
| 2–5 | `alcoia-group/alcoia` (extension) |
| 6 | New repo: `alcoia-group/alcoia-console` |

Do them **in this order.** Each phase depends on the one before it existing, not on it being
deployed anywhere real.

---

## Why nothing here needs the domain or real payment

Say this out loud once, because it's the thing causing the "lost" feeling: **you can build and test
every single piece below on your own laptop, right now, today, for free.**

- **Email:** the server already has a dev fallback — when `POSTMARK_TOKEN` isn't set, the magic
  link prints to the server's console log instead of sending a real email. You copy it from the
  terminal. No Postmark account, no domain, no DNS needed yet.
- **Payment:** Creem has a test mode (`test-api.creem.io`, test API keys) that needs no business
  verification. Every checkout in this roadmap uses test mode. Nobody is charged anything, ever,
  until you deliberately swap to live keys much later.
- **Webhooks:** Creem's webhook needs a public HTTPS URL to call. Your laptop doesn't have one. The
  standard fix is **ngrok** (`ngrok http 3000` or whatever port your server runs on) — a free,
  temporary tunnel that gives you a public URL for the length of your dev session. This is not a
  domain purchase. It's a five-minute setup you redo each time you test.
- **The console and the marketing site:** both just run locally (`localhost:3000`,
  `localhost:5173`, whatever) while you build them. They only need a real domain when you're ready
  to launch to actual users.

**The only two things you need to install, both free:** ngrok, and a Creem account in test mode
(you likely already have this from running S11).

Once every phase below works against localhost + ngrok + test mode, *then* — much later, not part
of this roadmap — you buy the domain, point DNS at your hosting, verify Postmark, and flip Creem to
live keys. That's a "go live" checklist, not a building checklist. Don't think about it yet.

---

## Phase 0 — five-minute local setup

Do this once, before Phase 1.

1. Run the server locally: `npm start` (or however `alcoiaServer` runs). Confirm `/health` responds.
2. Confirm `POSTMARK_TOKEN` is **unset** in your local `.env` — you want the console-log fallback,
   not real email, while building.
3. Install ngrok if you don't have it. Run `ngrok http <your-server-port>`. You'll get a URL like
   `https://abcd1234.ngrok.io`. Keep that terminal open while testing.
4. In your Creem **test-mode** dashboard, set the webhook URL to
   `https://abcd1234.ngrok.io/api/billing/webhook`. You'll have to update this each time ngrok
   gives you a new URL, unless you pay for a static ngrok domain — not required, just mildly
   annoying on free tier.
5. Confirm you're using Creem's **test** API key (`ck_test_...`) in your local `.env`, not a live
   one.

You're now fully set up to build and test everything below with no money spent.

---

## Phase 1 — the magic-link landing page

**Repo: `alcoia-group/alcoiaWeb`.**

**What this is and why it has to live here, in plain terms.** When a reader clicks the sign-in link
in their email, that link has to open in a normal browser tab — it cannot open extension code
directly, because email clients and Chrome won't allow that, and it would break entirely on a phone.
So the link points at a page on your website. That page's only job is to say "verify this link" to
the server, get back a short code, and hand that code to the extension. Then it can show a friendly
"you're signed in, you can close this tab" message.

This page can be built and tested entirely on `localhost` right now — you do not need the site
deployed anywhere.

```
Read the site repo's own conventions first — build.js, the CSS token system, the paper/ink
palette — and match them. This is one small page, not a redesign.

Task: a magic-link verification landing page at a route like /auth/verify (confirm the actual path
against what the server's magic-link email will point at — read alcoiaServer's auth route to get
this exactly right, do not guess).

Do:
- Read the token/code from the URL the email links to (the server puts a VERIFICATION token in
  this URL when it sends the email — this is fine, it's single-use and short-lived, and is
  DIFFERENT from the short-lived EXCHANGE CODE described below, which must never appear in a URL).
- On page load, POST that verification token to the server's verify endpoint. The response body
  contains a short-lived one-time CODE. Keep this code in memory only — a JS variable, never
  written to the URL, never logged, never sent to any analytics.
- Hand that code to the extension using:
    chrome.runtime.sendMessage(EXTENSION_ID, { code })
  This is Chrome's mechanism for one specific web page to message one specific installed
  extension, with no prior relationship needed. EXTENSION_ID will differ between your local dev
  build and the eventual Web Store release — make it a config value, not a hardcoded string, and
  say clearly in your report where that config lives and how to update it.
- Show three states clearly: verifying, success ("you're signed in — you can close this tab"),
  and failure (expired or already-used link, with plain language, no jargon).
- If sendMessage fails or nothing responds (meaning: no extension is installed, or it's an
  extension ID mismatch), show a state offering to install the extension. Do not show a raw error.

Do not:
- Put the exchange code in the URL, in a query string, in localStorage, or anywhere it could
  outlive the few seconds this page needs it for.
- Build any extension-side code. This page's only job is verify → get code → sendMessage. What
  happens after the extension receives it is a separate repo's problem.
- Add any new build dependency. This is a static page using what the site already has.

Verify and report actual output:
  Run the site locally, manually trigger a magic-link request against your local server (Phase 0
  setup), copy the printed link from the server's console log, open it, and confirm each of the
  three states renders correctly. Report exactly what you did, since this cannot be meaningfully
  unit tested — it's a real end-to-end click-through.

Report what changed, what you verified, and what you assumed. Then write a commit message in chat.
Do not commit, do not push.
```

---

## Phase 2 — Extension: sign-in and the handoff (E1)

**Repo: `alcoia-group/alcoia`.** Depends on Phase 1 existing (even just running locally).

```
Read CLAUDE.md fully first, then SERVER-ARCHITECTURE.md §4, and read src/shared/install-token.js
in full before writing anything — it is the pattern to follow: a small, dependency-light module
with a header comment explaining why it's shaped the way it is, not just what it does.

Task: a sign-in screen using magic-link auth (S3), receiving the handoff from the Phase 1 landing
page.

THE HANDOFF, so you build against a real contract rather than inventing one:

- The Phase 1 landing page (a different repo, alcoiaWeb) verifies the emailed link, gets a
  short-lived one-time CODE from the server, and sends it to this extension via
  chrome.runtime.sendMessage(EXTENSION_ID, { code }). You do not build that page. You build the
  receiving end.
- Declare "externally_connectable" in manifests/base.json. For local development, match against
  your local site's dev URL (e.g. "http://localhost:5173/*" — use whatever port alcoiaWeb actually
  runs on locally); this must be swapped to the real https://alcoia.app/* origin before any real
  launch. Comment this clearly as a dev-vs-prod value, and DO NOT assume the domain is live —
  it is not, and this whole roadmap is designed to work without it.
- Add a chrome.runtime.onMessageExternal listener in background.js (content scripts cannot receive
  this). It takes the code and calls the server's exchange endpoint to trade it for a real
  extension-kind session.
- REJECT any message from an origin that doesn't match your externally_connectable list. This is
  the only thing stopping an arbitrary web page from trying to hand you a fake code.
- "No extension installed" is not something you detect or handle here — it's what happens on the
  OTHER side (the landing page) when sendMessage fails. Nothing to build for it in this repo.

Do:
- A sign-in screen: email field, "send magic link" action, a clear "check your email" state.
- Store the resulting session the same way install-token.js stores its token: a small dedicated
  module, never a raw fetch scattered across call sites.
- Add "signed in as [email]" plus a sign-out control to the settings surface.
- Handle an expired or already-used code: the exchange endpoint will reject it — surface that
  honestly, don't silently retry.

Do not:
- Build the landing page. That's Phase 1, a different repo, already done or in progress.
- Build a password path. Magic link only.
- Let a signed-out state silently behave as if signed in anywhere. Every paid-feature check must
  ask "is there a valid session right now", not "does something exist in storage".
- Touch the free-tier install token flow — it's independent of all of this.

Tests:
- onMessageExternal rejects a message from an origin not in the allowed list.
- The full receive-code → exchange → session-stored path, mocked at the network boundary.
- An expired or already-used code fails cleanly and visibly.
- Sign-out clears the stored session and reverts the UI to signed-out.

HOW TO VERIFY END TO END, since this spans two repos and Phase 0's local setup:
1. Run alcoiaServer locally (Phase 0).
2. Run the Phase 1 landing page locally.
3. Load this extension unpacked in Chrome, note its dev extension ID, put that ID in your
   externally_connectable testing.
4. Trigger a magic link from the extension's sign-in screen, copy the printed link from the
   server's console log, open it in the same Chrome profile the extension is loaded in.
5. Confirm the extension ends up signed in.
Report exactly what you did and what you saw, step by step. This cannot be faked with a unit test
alone.

Verify and report actual output:
  npm run lint && npm test
  npm run test:browser

Report what changed, what you verified, and what you assumed. Then write a commit message in chat.
Do not commit, do not push.
```

---

## Phase 3 — Extension: entitlements client (E2)

**Repo: `alcoia`.** Depends on Phase 2. No domain, no payment involved — this only reads whatever
plan an account already has.

```
Read CLAUDE.md fully first. Phase 2 (E1) must be merged.

Task: one place the extension asks "what can this reader do", backed by GET /api/entitlements.

Do:
- A small module — same weight class as install-token.js — that fetches and caches entitlements,
  keyed on the current session. Expose hasFeature(name), not a bare tier string, matching the
  server's features[] shape — code elsewhere should never hardcode "if tier === 'reader'".
- Refresh on sign-in, on extension startup, and on a manual "refresh" trigger the upgrade page
  (Phase 4) will call.
- Signed-out or request-failed both resolve to free-tier capabilities. Fail closed — a failure
  must never grant something.
- No caching duration long enough that a cancelled subscription keeps working for days. State the
  TTL you chose and why.

Do not:
- Let any other module read chrome.storage for plan state directly. Everything goes through
  hasFeature().
- Build any paywall UI in this item — that belongs to whatever feature consumes this.

Tests:
- hasFeature returns correctly for each tier's feature set.
- A failed fetch resolves to free, not a stale cached "reader" state past its TTL.
- Refresh after a plan change is reflected without a full extension reload.

Verify and report actual output:
  npm run lint && npm test

Report, then a commit message in chat. Do not commit or push.
```

---

## Phase 4 — Extension: wire up the upgrade page (E3)

**Repo: `alcoia`.** Depends on Phases 2 and 3. **Uses Creem test mode throughout — no real charge
is possible or expected.**

```
Read CLAUDE.md fully first, and read src/popup/upgrade.js in full — every button is currently
disabled on purpose, with a comment explaining why. This item turns them on. Phases 2 and 3 (E1,
E2) must be merged.

THIS ENTIRE ITEM RUNS AGAINST CREEM TEST MODE. Confirm the server is configured with test API keys
(Phase 0). No real payment method or verification is needed to test this end to end. Do not add
any logic that behaves differently in test vs. live mode — that distinction lives entirely in
which API key the SERVER holds, never in this extension's code.

Do:
- Signed out: clicking any plan button routes to sign-in (Phase 2) first, remembering the chosen
  plan so checkout resumes immediately after landing back signed in.
- Signed in: clicking a plan calls the server's /api/billing/checkout (S11), receives a
  checkout_url, and opens it in a NEW TAB — not inside the popup, which cannot host an external
  hosted checkout page. This is a hard platform limit.
- After returning from Creem's checkout (test mode still shows a real-looking hosted page), the
  extension must not assume payment succeeded. Call Phase 3's refresh and reflect whatever the
  server actually reports. If the webhook hasn't landed yet (check your ngrok tunnel from Phase 0
  is still running), show an honest "processing" state.
- Once entitled, replace the disabled buttons with the real state: current plan, a manage/cancel
  link (Creem's customer portal).

Do not:
- Grant any UI-visible "upgraded" state before the entitlements client confirms it from the
  server.
- Try to open Creem's checkout inside an iframe or the popup's own window. It must be a real tab.

Tests:
- Signed-out click routes through sign-in and resumes checkout after.
- Signed-in click opens a new tab with the returned checkout_url.
- Return-from-checkout without a confirmed webhook shows "processing", not "upgraded".
- Once the server confirms the new entitlement, the page reflects it without a manual reload.

HOW TO VERIFY END TO END: with Phase 0's ngrok tunnel running and pointed at your server's webhook
endpoint in Creem's test dashboard, actually click through a real test-mode checkout using Creem's
documented test card numbers. Confirm the webhook arrives (check your server logs), confirm the
extension reflects the new plan. Report exactly what you did.

Verify and report actual output:
  npm run lint && npm test
  npm run test:browser

Report, then a commit message in chat. Do not commit or push.
```

---

## Phase 5 — Extension: seats, classes, disclosure (E4)

**Repo: `alcoia`.** Depends on Phases 2 and 3 (not Phase 4 — this doesn't need billing, just an
account).

```
Read CLAUDE.md fully first, then ALCOIA-PLATFORM-SPEC.md §6. Phases 2 and 3 must be merged.

Task: accept a class invitation, and show the disclosure screen. The disclosure screen ships
FIRST inside this item, not after.

Do:
- Accept an invite: a link or a code, calling S5's accept-invite endpoint.
- BEFORE completing the join, show the disclosure: the instructor sees aggregate results only,
  nothing individual, unless the student explicitly submits a receipt. Shown at the moment of
  joining, in plain language. The join does not complete until it has been shown.
- After joining: which class, which org, a way to leave (releases the seat), and what leaving does
  to access (reverts to free).
- Refresh entitlements (Phase 3) after a successful join — holding a seat grants Reader
  automatically.

Do not:
- Let a join complete without the disclosure having been shown.
- Build assignment notices in this item — separate, smaller item, later.
- Build anything LTI-specific.

Tests:
- A join cannot complete without the disclosure screen having been rendered.
- Leaving a class reverts entitlement to free and the extension reflects it.
- An invalid or expired invite fails cleanly.

Verify and report actual output:
  npm run lint && npm test
  npm run test:browser

Report, then a commit message in chat. Do not commit or push.
```

---

## Phase 6 — The admin console (new repo)

**This repo does not exist yet.** Everything below starts with creating it. Runs entirely on
`localhost` while building — no deployment needed for any of this.

**Why the console's sign-in is simpler than the extension's.** The server (S3) built two kinds of
session: `console` and `extension`. The `extension` kind needed the whole code-handoff dance from
Phases 1–2 because a web page can't write into extension storage. The `console` kind has no such
problem — it's an ordinary website, so verifying a magic link returns a normal session directly, no
handoff, no `sendMessage`, no extension ID. This is most of why the extension work felt hard and
the console work won't.

### C0 — Scaffold the console repo

```
Task: create alcoia-group/alcoia-console, a new private repo. This is a plain web app that talks
to alcoiaServer's API — it holds no business logic of its own, matching the pattern already
established: "the client never decides entitlement, it asks the server."

Do:
- Propose a stack in your report before committing to one: a small React + Vite single-page app is
  a reasonable default given this needs routing, forms and live data, but state your reasoning and
  flag if you think something simpler fits better given the rest of this project's minimalism.
- Basic routing, a page that calls the server's /health endpoint and displays the result, and a
  sign-in page shell (built out in C1).
- Read alcoiaServer's CLAUDE.md and SERVER-ARCHITECTURE.md fully before writing anything — this
  console is a client of that API and must not duplicate or reimplement any of its logic.
- Set up local dev against your Phase 0 local server (a .env with the API base URL, defaulting to
  localhost).

Do not:
- Add authentication yet — that's C1.
- Add any billing, seat, or aggregation logic — those are later items.

Verify and report actual output. Report, then a commit message in chat. Do not commit or push.
```

### C1 — Sign-in

```
Read CLAUDE.md and SERVER-ARCHITECTURE.md §4. C0 must exist.

Task: magic-link sign-in for the console, using the SIMPLER console-kind session (no code handoff
needed — see the note above this section for why).

Do:
- Email field, "send magic link" action, "check your email" state.
- A verify page/route: the emailed link points here, this page calls the server's verify endpoint,
  and — for console-kind links — the server returns a real session directly. Store it (a cookie or
  local storage, your call, argue for it) and redirect into the app.
- Sign-out.
- A logged-in shell: nothing behind it yet except a placeholder "signed in as X" page.

Do not:
- Build any code-exchange flow. That complexity belongs to the extension only.
- Add a password path.

Verify with a real click-through against your local server (Phase 0's console-log email fallback),
exactly as Phase 2 was verified. Report what you did.

Report, then a commit message in chat. Do not commit or push.
```

### C2 — Seats: invite, roster, release

```
Read CLAUDE.md fully first. C1 must exist.

Task: the seat management screen, backed by S5.

Do:
- Create an org (if the signed-in account doesn't have one) and a class.
- Invite students — link and email modes, per ALCOIA-PLATFORM-SPEC.md §6. Surface max_joins,
  expires_at and revocation clearly — these exist specifically so an open link can't drain the
  seat pool, make that visible to the instructor setting it up.
- A roster: who's in, seat count used vs. available (instructor seat free and uncounted — confirm
  the server already excludes it and display accordingly).
- Release a seat.

Do not:
- Show any per-student reading data here. That's the aggregate view (C5) and it's anonymous by
  default — this screen is roster/seat management only.

Verify and report actual output. Report, then a commit message in chat. Do not commit or push.
```

### C3 — Classes: reporting mode

```
Read CLAUDE.md and ALCOIA-PLATFORM-SPEC.md §8 (reporting modes) fully first. C2 must exist.

Task: let an instructor choose anonymous or identified reporting mode at class creation.

Do:
- The choice is presented ONCE, at class creation, with the consequence stated plainly: anonymous
  cannot become identified later, and vice versa — a new class must be created to change it. Make
  this impossible to miss, not a small radio button with no explanation.
- identified mode is Teams/Institution only — the server already rejects it on Pilot; surface that
  rejection as a clear message, don't let the UI offer an option the server will refuse.
- Once any student has joined, grey out the mode entirely with an explanation, even though the
  server also enforces this — a UI that pretends the choice is still live invites confusion.

Verify and report actual output. Report, then a commit message in chat. Do not commit or push.
```

### C4 — Assignments: upload and create

```
Read CLAUDE.md fully first. C2 and C3 must exist.

Task: upload a document (PDF today; PPTX/DOCX will show as "processing not yet available" per the
server's honest documents.status field — do not hide this, surface it) and create an assignment
against a class.

Do:
- Document upload, a title, opens_at/closes_at as a WINDOW — not presented to students as a
  monitored countdown; this is the console side of that same UX principle.
- Show documents.status honestly. If it says unsupported, say so in the UI rather than implying it
  will work.

Verify and report actual output. Report, then a commit message in chat. Do not commit or push.
```

### C5 — The aggregate view

**The flagship screen — the entire institutional pitch depends on this one working well.**

```
Read CLAUDE.md and ALCOIA-PLATFORM-SPEC.md §8 fully first, especially the part distinguishing
"hard" from "confidently wrong" — they must never be merged into one number. C4 must exist, and you
need at least one assignment with real outcome data to build this against (create one via C4 and
have a few test accounts submit outcomes through the extension, or via direct API calls if the
extension side isn't ready yet).

Do:
- Trouble map over the document — visually distinct from the confidently-wrong signal, never
  merged. Argue your visual treatment in the report; this distinction is the most important design
  decision in the whole console.
- Question-level failure rates, each showing the passage it cites.
- Abandonment point.
- Completion beside comprehension, side by side — this contrast is the strongest single sentence
  in the product's institutional pitch, make sure it's visually prominent.
- Respect the minimum-cohort floor: below 5 distinct pseudonyms, show "insufficient data", never a
  computed percentage from too few people. The server already enforces this at the API level —
  confirm the console displays that response state correctly rather than trying to compute around
  it.

Do not:
- Show any per-student data in anonymous mode, ever, regardless of what the API technically
  returns.
- Build rankings, a leaderboard, or a single class-average "grade".

Verify and report actual output, ideally with a screenshot of real (test) data. Report, then a
commit message in chat. Do not commit or push.
```

### C6 — Receipt submissions inbox

```
Read CLAUDE.md fully first. C4 must exist.

Task: a list of receipts students have chosen to submit for an assignment (S8).

Do:
- List submissions, each showing the "unaltered since issued" wording — never "verified" or
  "authentic" anywhere in this screen's copy.
- Make clear in the UI that this list only contains what students explicitly chose to submit — it
  is not a report of who read what.

Verify and report actual output. Report, then a commit message in chat. Do not commit or push.
```

### C7 — Billing status

```
Read CLAUDE.md fully first. C1 must exist. Uses Creem test mode, same as Phase 4.

Task: show the org's plan, seat usage, and a link to Creem's customer portal for managing or
cancelling.

Do:
- Current plan, seats used vs. purchased.
- A link to Creem's hosted customer portal (S11 already exposes this) rather than building any
  billing management UI yourselves.
- Stub SSO configuration, self-hosted setup, and LTI connection status as "available on request" —
  per the standing decision to defer all three past launch. Do not build the real thing for any of
  them here.

Verify and report actual output. Report, then a commit message in chat. Do not commit or push.
```

---

## What "90%" looks like when you're done

After all of Phase 0 through C7:

- A reader can install the extension, sign in with no password, and see it work.
- They can upgrade through a real (test-mode) Creem checkout and see paid features unlock.
- An instructor can sign into a brand-new console, create an org and class, choose a reporting
  mode, invite students by link or email, upload a document, create an assignment, and — once
  students have read it through the extension — see the aggregate trouble map with hard and
  confidently-wrong passages shown as genuinely different things.
- A student can join that class through the extension, see the disclosure screen before anything
  else, read the assignment, and optionally submit a receipt the instructor can see individually.
- None of this required a domain, a live payment, or a verified email sender.

## What's still deferred after all of this — not part of this roadmap

- **Going live**: buying `alcoia.app`, DNS, Postmark domain verification, swapping Creem to live
  keys. A short checklist once you're ready, not engineering work.
- **SSO** — built against the first customer who asks.
- **LTI-side extension UI** — the small "recognise an LTI launch, show the disclosure automatically"
  item. Needs Phase 5 done first.
- **DOCX/PPTX processing** — independent, can happen anytime.
- **Self-hosted Institution deployments** — a sales unlock, not a roadmap item.
- **Running the server's 57 skipped Postgres tests against a real database** — still the single
  biggest unverified risk in the whole system, orthogonal to this roadmap but should happen before
  any of this is trusted with a real paying class.

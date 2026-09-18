# alcoia — Going-Live Checklist

Complete this in order. Each section has hard blockers (must be done before any user
touches the product) and soft items (should be done before wide promotion). Nothing
here is optional for the hard blockers. Items marked [LAWYER] require legal review
and cannot be self-certified.

---

## SECTION 1 — Infrastructure: make the server real

### 1.1 — Flip NODE_ENV to production [HARD BLOCKER]
Currently set to 'development' on Render to unblock deployment testing. In development
mode the server silently uses fallbacks: no real AI calls, email logged not sent, files
kept in memory rather than persisted to R2. Every feature that looks like it works is
lying.

In Render's environment variables:
- Change NODE_ENV from 'development' to 'production'
- Do NOT do this until steps 1.2 through 1.5 are complete — the server will refuse
  to boot in production without real credentials

### 1.2 — AI provider setup [HARD BLOCKER]

**Decision updated:** Groq is genuinely free-free (no credit card required, confirmed
from direct account use). The two-phase Gemini/Groq plan described in an earlier
draft is no longer needed — Groq is primary for beta and beyond.

Current state: models updated to openai/gpt-oss-20b (cheap tier) and
openai/gpt-oss-120b (smart tier), with qwen/qwen3.6-27b as the 429 fallback for
both — all in config, all tested, all deployed. The fallback fires on 429 only,
drawing from a separate rate-limit pool.

**Real free-tier ceiling to size your beta cohort against:**
- openai/gpt-oss-20b and openai/gpt-oss-120b: 1,000 requests/day, 200,000 tokens/day
- qwen/qwen3.6-27b fallback: 1,000 requests/day (separate pool)
- Combined effective ceiling: ~2,000 calls/day, ~400,000 tokens/day
- At ~500 tokens/call and 20 calls/user/day: roughly **40 active daily users**
- Size your beta invite list around 40–50 active daily users, not 500

**What to do:**
- Create a production API key at console.groq.com
- Add to Render environment: GROQ_API_KEY=[real key]
- Verify: make a real AI call through the extension on the live server and confirm
  a question is actually generated (not dev-mode logged) — this only works after
  1.1 (NODE_ENV flip) is also done

**Note on qwen model name typo:** src/config.js has GROQ_SMART_MODEL_FALLBACK
defaulting to qwen/qwen3.8-27b — this should be qwen/qwen3.6-27b. Fix this one
line before deploying or the smart-tier fallback will fail with a model-not-found
error.

### 1.3 — Configure Postmark for real email delivery [HARD BLOCKER]
- Verify the alcoia.app sending domain in Postmark (DNS records: SPF, DKIM, DMARC)
- This is required for magic links to actually arrive in users' inboxes
- Add to Render environment: POSTMARK_TOKEN=[real token]
- Update MAGIC_LINK_BASE_URL on Render to: https://alcoia.app/auth/verify
- Verify: request a magic link on the live site, confirm it arrives in the inbox
  and the link works correctly

### 1.4 — Configure Cloudflare R2 for real file storage [HARD BLOCKER]
- Confirm R2 bucket is private (not publicly accessible)
- Add to Render environment: STORAGE_* (R2 account ID, access key, secret key,
  bucket name — confirm the exact variable names from src/config.js)
- Verify: upload a document to an assignment on the live console, confirm it stores
  in R2 (not in memory, which silently loses it on server restart)

### 1.5 — Consolidate the two marketing-site origin variables [SOFT — before wide promotion]
WEB_APP_ORIGIN and MARKETING_SITE_ORIGIN both mean "the marketing site's origin" but
are used by different routes — this caused a real CORS bug during testing. Before
promoting the product widely, consolidate these into one clearly-named variable in
src/config.js and src/app.js. Low urgency but will cause another CORS bug if anyone
changes one and forgets the other.

---

## SECTION 2 — Domain: move off the shared subdomain

### 2.1 — Purchase alcoia.app [HARD BLOCKER]
Not purchased yet. Everything canonical points here; buy it.

### 2.2 — Configure DNS [HARD BLOCKER]
- Marketing site (alcoiawebsite.cedricjulien77.workers.dev) → alcoia.app and www.alcoia.app
  via Cloudflare Pages custom domain
- Console (alcoiaconsole.cedricjulien77.workers.dev) → console.alcoia.app
  via Cloudflare Pages custom domain
- Server (alcoiaserver.onrender.com) → api.alcoia.app
  via Render custom domain settings
- Confirm HTTPS/TLS is issued and working for all three subdomains

### 2.3 — Update all environment variables to real domains [HARD BLOCKER]
After DNS is live, update on Render:
- WEB_APP_ORIGIN → https://alcoia.app
- MARKETING_SITE_ORIGIN → https://alcoia.app
- CONSOLE_ORIGIN → https://console.alcoia.app
- MAGIC_LINK_BASE_URL → https://alcoia.app/auth/verify
- BILLING_SUCCESS_URL → https://console.alcoia.app/billing/success (or wherever
  the post-checkout landing page actually lives — confirm this exists)
- BILLING_CANCEL_URL → https://alcoia.app/pricing
- LTI_LAUNCH_URL → https://api.alcoia.app/api/lti/launch

Also update in alcoiaWeb's build config (Cloudflare environment variables):
- API_BASE_URL → https://api.alcoia.app
- CONSOLE_URL → https://console.alcoia.app

And in alcoia extension's config.js:
- BACKEND_ORIGIN → https://api.alcoia.app
- WEB_APP_ORIGIN → https://alcoia.app

### 2.4 — Verify Brave Shields resolves with real domain [SOFT — before wide promotion]
The onrender.com subdomain is blocked by Brave's built-in ad blocker, confirmed via
the Adblock devtools panel (Blocked: true, DidMatchRule: true). The working theory is
that this is a shared-domain filter-list rule that will stop matching once api.alcoia.app
is the server address.

Verify: after DNS switch, open https://console.alcoia.app in Brave with Shields ON.
Check the Network tab for any ERR_BLOCKED_BY_CLIENT on requests to api.alcoia.app.
If still blocked: check brave://adblock URL tester against api.alcoia.app/health to
identify the exact matched rule, and investigate whether it is domain-specific or pattern-
based. Do not assume the switch resolved it — verify it directly.

---

## SECTION 3 — Billing: switch to live Creem

### 3.1 — Set up live Creem products [HARD BLOCKER for paid tiers]
All billing is currently on test-mode keys with test product IDs. Before accepting
real payment:
- Create production products in Creem's live dashboard:
  - Reader (annual): $59/year
  - Student (annual): ~$29/year (confirm the exact price)
  - Teams (monthly per seat): $4/seat/month, minimum 25 seats (uses the quantity
    mechanism already built — confirm Creem's live product is set up with quantity)
  - Annual Teams: same product, annual pricing (the toggle UI exists; just needs
    the live product ID)
- Copy the live product IDs
- Update Render environment variables:
  - CREEM_API_KEY → live key (not creem_test_ prefix)
  - CREEM_API_BASE_URL → https://api.creem.io (not test-api)
  - CREEM_PRODUCT_ID_READER → live product ID
  - CREEM_PRODUCT_ID_STUDENT → live product ID
  - CREEM_PRODUCT_ID_TEAMS → live product ID

### 3.2 — Register live webhook [HARD BLOCKER for paid tiers]
- In Creem's live dashboard: add webhook endpoint https://api.alcoia.app/api/billing/webhook
- Copy the signing secret
- Add to Render: CREEM_WEBHOOK_SECRET → live secret
- ngrok is no longer needed; the real server URL is public

### 3.3 — Verify end-to-end checkout with live keys [HARD BLOCKER for paid tiers]
- Complete a real Reader checkout with a real card (not a test card)
- Confirm: webhook fires, plan updates in the database, extension reflects new entitlement
- Close browser entirely, reopen — confirm entitlement persists across sessions
- Let session approach TTL — confirm entitlement refresh works
- Confirm upgrade page shows correct plan state on first load after checkout, not just
  after a reload triggered by the checkout return

### 3.4 — Configure auto-renewal disclosures [HARD BLOCKER — legal requirement]
California ARL (as amended by AB 2863, eff. July 1, 2025) requires:
- Clear disclosure of renewal frequency, amount, and cancellation method
  BEFORE the user completes checkout
- Annual reminder email before each renewal
- Same-channel cancellation (if they signed up online, they must be able to cancel online)
- Proof of consent retained for 3 years or 1 year post-termination, whichever is longer

Confirm Creem handles renewal reminder emails, or build them via Postmark. Confirm
the cancellation path is accessible from the console's Settings → Plan section.
[LAWYER] — confirm the checkout flow's disclosure language satisfies California ARL
and any other applicable state laws. The Terms of Service draft must also describe
auto-renewal clearly and prominently.

---

## SECTION 4 — Production database

### 4.1 — Run all migrations on the production Neon database [HARD BLOCKER]
The startup command (node-pg-migrate up && node src/server.js) runs migrations
automatically on deploy. Confirm all migrations have run by checking the pgmigrations
table on Neon:

Connect via Neon's SQL editor and run:
SELECT name, run_on FROM pgmigrations ORDER BY run_on;

Confirm every migration is present, including the most recent ones (scroll_sessions,
add-platform-admin-flag, etc.). If any are missing, the server likely failed to boot
on a previous deploy and rolled back silently.

### 4.2 — Run the full Postgres test suite [SOFT — before wide promotion]
The test suite has 64–84 Postgres integration tests consistently skipped because no
TEST_DATABASE_URL was available during development. Run them once against the production
database (or a staging branch on Neon) to catch any schema assumption the tests make
that doesn't match the real production schema:

TEST_DATABASE_URL=[production connection string] npm test

Review any failures before promoting the product.

### 4.3 — Record scroll kinematics collection start date [SOFT — day one]
The scroll_sessions table baseline corpus is only useful if you know when "all users
were human" began. On the day of launch, record in CLAUDE.md:
"Baseline scroll collection started: [date]. All users at this point are verified human.
Collection label: baseline_v1."

---

## SECTION 5 — Legal pages [HARD BLOCKER — all items]

These must be reviewed by a lawyer before launch. What exists are drafts generated
from factual inputs. No draft should be published as-is.

### 5.1 — Privacy policy [LAWYER REVIEW REQUIRED]
The draft (generated during item 15b-6) covers the core frameworks correctly. Before
lawyer review, confirm these specific items are accurate in the draft:

- The COPPA section states that under-13 users are blocked at account creation (self-
  attestation required), not that a school-consent pathway exists — this was changed
  from the original draft. Confirm the draft reflects this.
- The scroll kinematics section (from DATA-TRANSPARENCY.md) must be added — it was not
  in the original draft because the collection was built afterward. Add it before
  sending to the lawyer.
- The "accurate statement" from LEGAL-BRIEF.md §1 must appear verbatim:
  "alcoia never sees your browsing. Material your instructor assigns is uploaded by them
  deliberately, and your reading of it is aggregated anonymously."
- Confirm no accuracy figures (0.851, 88%, etc.) appear anywhere in the policy or
  on the site — LEGAL-BRIEF.md §5 prohibits these.

### 5.2 — Terms of service [LAWYER REVIEW REQUIRED]
The draft (generated during item 15b-7) covers the key areas. Confirm before legal review:
- The auto-renewal disclosure is prominent, not buried in a subsection
- Section 5 (instructor upload) clearly states uploader responsibility for content,
  a DMCA/takedown contact, and that uploaded documents are deleted at class close
- Receipts section uses only "unaltered since issued" — never "verified" or "authentic"
- The under-13 section matches the privacy policy (self-attestation block, no school
  consent pathway)
- Cancellation terms match the actual Creem implementation

### 5.3 — Imprint / legal contact page [LAWYER REVIEW REQUIRED]
Currently shows "address available on request" by email. In Germany, Austria, and several
other EU jurisdictions, a full legal address is required in the Impressum. Before promoting
to EU audiences, confirm what jurisdiction applies and whether email-only satisfies the
local Impressum requirement. If a physical address is required, add it.

### 5.4 — DMCA / copyright takedown process [HARD BLOCKER — alcoia is now a content host]
Alcoia accepts instructor-uploaded documents. This makes it a content host. LEGAL-BRIEF.md
§1 and §6 both flag this as requiring a takedown process. Before Teams/Classrooms is
promoted:
- Designate a DMCA agent (required in the US to receive safe harbor protection)
- Register the agent with the US Copyright Office (at copyright.gov/dmca-directory)
- Add the takedown contact to the Terms of Service and to a dedicated /legal/dmca page
- Create an internal process for responding to takedown notices within the legal deadline
[LAWYER] — confirm this is correctly structured for DMCA safe harbor

### 5.5 — Chrome Web Store privacy disclosure [HARD BLOCKER]
The Web Store independently requires:
- A privacy policy linked in the Developer Dashboard — confirm the live privacy policy
  URL is set there
- A completed data-handling certification in the Dashboard Privacy tab — this must match
  the privacy policy exactly; reviewers cross-reference them
- A Limited Use statement on the website or privacy policy explicitly stating that data
  is used only for the extension's disclosed single purpose
- In-product prominent disclosure and consent before any data collection

This is a separate requirement from everything else. A rejected Web Store listing is an
existential distribution risk. Confirm all four items before submitting.

### 5.6 — Acceptable use policy [SOFT — before wide promotion]
Not yet drafted. Should cover: prohibited uses (misuse of receipts to claim reading that
didn't happen, automated scroll tools, attempting to circumvent detection, academic
dishonesty facilitation), and consequences (account termination, notification to instructor).
[LAWYER] — brief document, but should be reviewed

---

## SECTION 6 — Extension: Chrome Web Store submission

### 6.1 — Extension store listing assets [HARD BLOCKER]
The Web Store requires specific assets that may not exist yet:
- 1 promotional tile: 440×280px
- 1 large promotional image: 920×680px (optional but strongly recommended)
- At least 1 screenshot: 1280×800 or 640×400px, showing the extension in use
- A demo video (optional but increases install rate significantly)
- A store description: plain language, no keyword stuffing, no claims that can't be
  substantiated (especially no accuracy figures per LEGAL-BRIEF.md §5)

All images must be real coded UI screenshots or components — not AI-generated images
(per the project's standing convention and per Google's own review guidelines).

### 6.2 — Extension permissions justification [HARD BLOCKER]
The manifest declares: storage, activeTab, tabs, webNavigation, host_permissions
(<all_urls> and file:///*). The Web Store requires a plain-language justification for
each permission in the privacy certification. Confirm these justifications are prepared
and match actual usage:
- storage: session data, settings, install token
- activeTab: reading detection on the current page
- tabs: detecting page navigation to wire SPA detection
- webNavigation: SPA detection
- <all_urls>: required for reading detection on any page the user visits
- file:///: required for local PDF detection

### 6.3 — Submit for review [HARD BLOCKER]
Google's review process typically takes 1–7 business days for a new extension. Submit
early, before the planned launch date, to account for review time and potential rejection
requiring resubmission.

After approval, update the Web Store link on the marketing site's hero and in the
extension README (currently a placeholder).

---

## SECTION 7 — Functional verification on real infrastructure

### 7.1 — End-to-end sign-in flow [HARD BLOCKER]
On the real live site (not localhost):
- Click "Run a pilot" on the marketing site
- Complete the email form
- Confirm the magic link arrives in a real inbox (not the server's dev log)
- Click the link — confirm it lands on the console signed in, not on the marketing
  site homepage

**The console health indicator is currently red on the hosted version, and
in-console sign-in redirects to the marketing homepage after verification.**
Both are the same root cause: the server is in dev mode (NODE_ENV=development)
and the real domain isn't set yet, so the server's health endpoint and auth
redirect both point at localhost or behave as dev fallbacks. Neither is a separate
bug to hunt — both resolve automatically once Section 1 (real credentials) and
Section 2 (real domain + DNS) are complete. Do not spend time debugging these
before Sections 1 and 2 are done; they will fix themselves.

The primary pilot flow (marketing site → console) must work end-to-end before
beta invites go out. The in-console sign-in redirect (signing in from inside the
console itself → lands on marketing homepage) is a known, lower-priority bug that
can be addressed after launch.

### 7.2 — Subscription persistence [HARD BLOCKER]
After completing a real Creem checkout with live keys:
- Close the browser entirely (not just the tab)
- Reopen and navigate to the extension's upgrade page
- Confirm the plan shows correctly without needing to sign in again
- Confirm hasFeature() returns the correct entitlement
- Note: chrome.storage.local is wiped when the extension is reloaded in developer
  mode — this is expected dev behavior, not a production bug

### 7.3 — Seat card / subscription display verification [SOFT]
The Reader card's seat-vs-subscription display was fixed but not definitively confirmed:
- Sign in with an account that has an active subscription
- Join a class (with a real invite token)
- Open the upgrade page
- Confirm it shows BOTH the subscription state AND the class seat state
- Previously: only the subscription state appeared; the seat mention may not be firing

### 7.4 — Assignment reading flow end-to-end [HARD BLOCKER for Teams/Classrooms]
With a real instructor account and a real student account:
- Instructor: create a class, upload a PDF, create an assignment
- Student: accept the invite (goes through disclosure screen), open the assignment
- Student: read the PDF — confirm the detection fires and a question appears
- Confirm the outcome reaches the server (check server logs)
- Instructor: open the aggregate view — confirm data appears after the 5-pseudonym
  floor is met

### 7.5 — Correct-answer silence [SOFT — before wide promotion]
Confirm that answering a question correctly at the adversarial and free-recall levels
produces only a brief confirmation, not an explanation. This was a deliberate design
decision that may have drifted. If it has regressed, fix it before launch.

### 7.5b — Wrong-answer feedback mechanism [CLOSED — verified intact]
Verified via a full code audit (not a click-through, since no live AI key existed at
verification time): explanation-on-failure is real and grounded in the actual passage
span, not generic (question-card.js's revealAnswer() and offerExplanation(), both
scoped to question.span). Correct-answer silence confirmed unbroken. Substate does
not yet vary the explanation — confirmed as expected, tracked separately in
FUTURE-IMPLEMENTATIONS.md items 8 and 12, not a launch blocker. Question difficulty
does not escalate on failure (epistemic-engine.js's nextLevel() holds steady at
recognition/free_recall on a miss) — the real gap is narrower than first assumed:
no floor exists below recognition for a reader who repeatedly fails there. Noted in
FUTURE-IMPLEMENTATIONS.md item 8 as the natural place to add that floor later.
Existing test coverage (tests/question-card.test.js) already protects this mechanism.
No action needed before launch.

### 7.6 — Contact form delivery [HARD BLOCKER]
After POSTMARK_TOKEN is set and NODE_ENV is production:
- Submit a real message through the contact form on the live site
- Confirm it arrives at hello@alcoia.app
- Confirm the server logs do not show the TO address in any response

---

## SECTION 8 — Social and launch preparation

### 8.1 — Extension redirect to /contact [SOFT — after alcoia.app is live]
The extension popup's home mode does not yet link to the contact page. Once alcoia.app
is confirmed live and the contact form is working, run the extension contact redirect
prompt (SMALL-ADDITIONS.md, Prompt 3). This is a small addition that significantly
improves support discoverability.

### 8.2 — Student honor system decision [SOFT — before promoting Student tier]
The Student plan checkout has no verification that the buyer is actually a student —
it is currently honor-system. This was explicitly deferred without full understanding
of the tradeoff. Before promoting the discounted Student tier widely:
- Understand: the question is whether a lightweight gate (e.g. requiring a .edu email
  address at checkout) is worth the friction cost of excluding international students
  who use non-.edu addresses and students at institutions that don't use .edu
- Honor-system is a defensible and common choice at this stage; it just needs to be
  a deliberate one, not an overlooked one

### 8.3 — Pricing page — unbuilt features [SOFT — before launch]
LEGAL-BRIEF.md §8 is explicit: features not yet shipped must not appear on the pricing
page. Check the current pricing page against what is actually deployed:
- DOCX support: not built. Remove or move to roadmap.
- PPTX support: partially built. State the actual status accurately.
- Annual Teams plan: toggle UI exists, live Creem product not yet created. Either
  create the product (do this as part of 3.1) or mark it as coming soon.
- SSO: not built. Should not appear on the Pilot pricing tier specifically — this
  was confirmed removed, but verify.

### 8.4 — Pilot "one term" claim [SOFT — before launch]
The marketing site says "one class, one term" for Pilot. The server now enforces the
one-class limit. But "one term" has no enforcement — a Pilot org stays free indefinitely.
Decide before launch: is this a real commitment or is Pilot genuinely free forever?
If it's genuinely free forever, update the copy to say so ("free for pilot programs, 
no time limit"). If there is a time limit, build the enforcement. Do not leave a
promise the system doesn't keep.

### 8.5 — PostHog analytics [LAUNCH DAY — install before first real user]
PostHog must be installed before beta invites go out — not after — because you
cannot retroactively capture the first users' behavior, and early-user data is
the most valuable data for understanding where people drop off, get confused, or
succeed.

**What to install:**
- PostHog's JavaScript snippet on the marketing site and the console (two separate
  installs, two separate project keys so you can distinguish site traffic from
  console activity)
- Page view tracking only on day one — no custom events yet. This gives you
  install-to-use funnels and drop-off points without any risk of accidentally
  capturing sensitive reading content.

**What NOT to track (ever, without explicit privacy review):**
- Any passage text or document content
- Individual reading session behavior (covered by the privacy architecture)
- Anything that would contradict the privacy policy's "no individual reading history"
  commitment

**How to install (five minutes per surface):**
- Sign up at posthog.com (free tier: 1M events/month, plenty for beta)
- Create two projects: "alcoia marketing" and "alcoia console"
- Add the HTML snippet to alcoiaWeb's base template and alcoiaConsole's index.html
- Add POSTHOG_KEY as an environment variable in Cloudflare Pages for each

**What to add after the first week of real data:**
- Funnel: homepage → "Run a pilot" → sign-in → console dashboard (where do people
  drop off?)
- Event: "first question answered" (the moment the extension actually works for
  someone for the first time)
- Do NOT add reading-behavior events — those stay in the server's own aggregate
  system, not in a third-party analytics tool

### 8.6 — Record the baseline collection start date [SOFT — day one]
On launch day, record in CLAUDE.md: "Baseline scroll kinematics collection started:
[date]. All users at this date are verified human. Collection label: baseline_v1."
This metadata is essential for the anti-AI-scroll classifier to be meaningful.

### 8.8 — Waitlist invite flow [BEFORE FIRST BETA INVITE]
The waitlist server infrastructure is built and tested. The invite endpoint
(POST /api/waitlist/invite, platform-admin-gated) works via API. What doesn't
exist yet is a console UI for managing and sending invites — currently you'd
have to call the API manually via curl or a script.

Before sending the first beta invites, build the admin invite UI in the console
(see prompt in EARLY-ACCESS-AND-DISTRIBUTION.md, EA-5 section). Until then,
you can send invites manually:

curl -X POST https://api.alcoia.app/api/waitlist/invite \
  -H "Authorization: Bearer [your-console-session-token]" \
  -H "Content-Type: application/json" \
  -d '{"emails": ["person@example.com"]}'

Get your session token from the console's localStorage after signing in:
localStorage.getItem('alcoia_session_token') in the browser console.

Also confirm: the waitlist invite email currently has no replyTo set — add
replyTo: hello@alcoia.app to compose-waitlist-invite-email.js for consistency
with the confirmation email (one-line fix).

 [SOFT — before launch, then ongoing]
The student and instructor quick-start guides (/guide-students, /guide-educators) were
built during item 15b-4. The instructor guide is believed complete. The student guide
may be lighter — confirm by reading it directly against what the extension actually
does today (assignments, receipts, quiz, highlights, self-report, settings).

This is not a one-time task. Every time a new user-facing feature ships — the
time-budget selector, teach mode, dynamic retrieval, differential intervention, or
anything else from FUTURE-IMPLEMENTATIONS.md that reaches real users — the relevant
guide page needs a corresponding update before or alongside that feature's release.
A shipped feature with no explanation in the guide is functionally undiscoverable to
most users.

Treat "update the guide" as a standing checklist item on every future feature prompt
from this point forward, not something to remember separately each time.

---

## SECTION 9 — Post-launch: institutional readiness (do not block launch on these)

### 9.1 — LTI handoff security review [BEFORE FIRST CANVAS/INSTITUTIONAL CUSTOMER]
The lti_launch_handoff_codes table stores the full computed payload verbatim, including
a real usable sessionToken, in plaintext for up to a 2-minute TTL. This was a deliberate
tradeoff (better than the token in a URL). Decide before the first real Canvas institution
whether to rework this to mint the session fresh at exchange time (same pattern as
extension_handoff_codes), which would mean no usable credential ever sits in the database.

### 9.2 — GDPR Data Processing Agreement [BEFORE FIRST EU SCHOOL CUSTOMER]
The current school addendum covers US FERPA + SOPIPA. EU schools additionally require
a full GDPR Article 28 DPA with Standard Contractual Clauses (SCCs) for the Groq
data transfer. Build this when the first EU school asks, not speculatively.
[LAWYER] — cannot be self-drafted

### 9.3 — Longitudinal outcome store [BEFORE INDEPENDENCE TRAJECTORY OR PERSONALIZATION FEATURES]
Deferred because it requires either storing real account_id (privacy concern) or an
explicit user opt-in with proper consent and a GDPR lawful basis analysis. Build only
after legal review confirms the right architecture. Do not build speculatively.

### 9.4 — CLA for external contributions [BEFORE ACCEPTING ANY EXTERNAL PULL REQUESTS]
Without a Contributor License Agreement, external contributions to the AGPL-3.0 extension
cannot be relicensed if the licensing strategy ever changes. Decide before accepting
the first external pull request whether a CLA is needed. A simple CLA (agreeing that
the contribution is licensed under the same terms as the project) can be managed via
cla-assistant.io with no significant overhead.

### 9.5 — WCAG 2.1 AA accessibility audit [BEFORE SELLING TO PUBLIC INSTITUTIONS]
The European Accessibility Act (enforceable since June 28, 2025) and US ADA Title III
both require WCAG 2.1 AA compliance for commercial web products sold to educational
institutions. Public universities in many jurisdictions require a VPAT (Voluntary Product
Accessibility Template) as a procurement condition. Commission a formal accessibility
audit before pursuing institutional sales.

### 9.6 — Two-variable CORS cleanup [SOFT — first maintenance window]
WEB_APP_ORIGIN and MARKETING_SITE_ORIGIN both mean the marketing site but are used by
different routes. This caused a real CORS bug once already. Consolidate into one
clearly-named variable in the first maintenance window after launch.

---

## SECTION 10 — Final confirmation before going live

Run through this list in order. Do not proceed to the next item until the current one
is confirmed. These are the absolute minimum for a safe launch.

☐ NODE_ENV = production on Render
☐ GROQ_API_KEY set and real AI calls working (verify a question generates, not logs)
☐ Qwen fallback model name typo fixed (qwen3.8-27b → qwen3.6-27b in config)
☐ Beta cohort size confirmed against real Groq free-tier ceiling (~40 active daily users)
☐ RESEND_API_KEY set on Render and Resend domain alcoia.app verified green in dashboard
☐ EMAIL_FROM set to noreply@alcoia.app on Render
☐ Magic links arrive in real inbox (not dev-mode logged)
☐ Contact form delivers email to hello@alcoia.app
☐ Waitlist invite email replyTo added (one-line fix in compose-waitlist-invite-email.js)
☐ hello@alcoia.app email routing active (Cloudflare → Gmail for now, Zoho for production)
☐ R2 storage configured and documents persisting across server restarts
☐ alcoia.app purchased ✅ and DNS pointing correctly ✅
☐ HTTPS/TLS working for alcoia.app ✅, console.alcoia.app ✅, server.alcoia.app ✅
☐ server.alcoia.app Worker proxy active ✅ and returning {"status":"ok"} ✅
☐ alcoia-keepalive cron job running (every 14 minutes) ✅
☐ api.alcoia.app reserved (TXT record) — do not route traffic here until API product built ✅
☐ developers.alcoia.app built and deployed (API documentation console)
☐ All environment variables updated to real domain names (server.alcoia.app not api.alcoia.app)
☐ Brave Shields confirmed NOT blocking server.alcoia.app (verify with shields on in Brave)
☐ Live Creem products created (Reader annual, Student annual, Teams monthly, Teams annual)
☐ Live Creem API key and base URL updated on Render
☐ Live webhook registered at https://server.alcoia.app/api/billing/webhook
☐ Real end-to-end checkout completed with real card, entitlement persists across browser restart
☐ Privacy policy reviewed by a lawyer and published at alcoia.app/privacy
☐ Privacy policy updated to include scroll kinematics collection section (DATA-TRANSPARENCY.md)
☐ Terms of service reviewed by a lawyer and published at alcoia.app/legal/terms
☐ DMCA agent registered at copyright.gov/dmca-directory
☐ Chrome Web Store listing submitted and approved (allow 1–7 business days)
☐ Web Store privacy certification completed in Developer Dashboard
☐ Web Store link updated on marketing site hero and extension README (currently placeholder)
☐ PostHog installed on marketing site and console (page views only, two separate projects)
☐ Waitlist page live at alcoia.app/waitlist ✅ — confirm count endpoint returning real numbers
☐ End-to-end assignment reading flow works on live infrastructure (real instructor + student)
☐ Scroll kinematics collection confirmed firing on live server (check server logs)
☐ Baseline collection start date recorded in CLAUDE.md
☐ Student guide page confirmed complete against actual shipped features
☐ Status page set up at status.alcoia.app (Instatus/Betterstack — do before wide promotion)
☐ Zoho Mail set up for hello@alcoia.app (replace Gmail forwarding before production)
☐ Product Hunt maker account created and karma-building started (do this now — takes weeks)
☐ Voice prompt system prompts added to all AI-facing routes (EA-1 style constraints)


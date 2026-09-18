# alcoia data collection — what it is, where it goes, how it will be used

This document is internal. It describes every data collection point in alcoia,
what the data is used for, and the constraints that govern it. It is the honest
record of what leaves a reader's device and what is done with it.

This document is also the source of truth for legal copy (privacy policy) and
for any future CLAUDE.md updates about what is and is not permitted.

---

## The governing principle

alcoia's privacy architecture is built around one constraint: **no individual reading
history on the server.** A reader's comprehension of specific passages, their answers
to specific questions, and their reading behavior on specific pages are not linked to
their identity in any persistent, retrievable way.

This is not a statement of intent. It is enforced structurally:
- Where data is stored linked to an individual (an account), it is limited to the
  minimum necessary to operate the service (email, plan, session tokens).
- Where data is stored for aggregate reporting or analysis, it uses a pseudonym that
  changes per assignment — so a reader's behavior in one class cannot be linked to
  their behavior in another, even by someone with full database access.
- Where data is collected for future research (scroll kinematics), it uses the same
  pseudonym system and is subject to the same cohort floor that protects instructor-
  facing reporting.

---

## Every data point, where it goes, and what it is used for

### 1. Account data
**What:** email address, hashed authentication credential, plan tier, plan expiry.
**Where stored:** `accounts` table, Neon Postgres, hosted on Neon's servers.
**Who can read it:** alcoia's server only. No instructor can read another user's email.
**How long retained:** until the account is deleted.
**Used for:** authentication (magic link delivery), entitlement checks (what features
  are available), billing (which Creem subscription is linked to this account).
**Not used for:** any form of behavioral profiling or reading history.

### 2. Session tokens
**What:** short-lived cryptographic tokens issued at sign-in.
**Where stored:** `sessions` table, Neon Postgres. Hashed at rest — the raw token
  never touches the database.
**How long retained:** until TTL expires (typically days to weeks depending on kind).
**Used for:** authenticating API requests.

### 3. Install tokens (free tier)
**What:** an opaque per-install token, not derived from any device identifier or IP.
**Where stored:** `install_tokens` table, Neon Postgres.
**Used for:** rate-limiting AI assist calls on the free tier.
**Not used for:** identifying a device, linking sessions, or any form of tracking.

### 4. Passage text (sent for question generation)
**What:** the text of the specific passage a reader slowed down on — typically one
  paragraph or sentence, not the full page.
**Where it goes:** sent from the extension to the server, forwarded to Groq (the AI
  inference provider) to generate a question and, if the reader answers incorrectly,
  an explanation.
**Stored:** NOT stored. Passage text is used to generate the question/explanation and
  then discarded. It is not written to any database table.
**Who sees it:** alcoia's server (transiently, in memory during request processing) and
  Groq (as an API request). Groq's data handling is governed by their privacy policy.
**Used for:** generating retrieval questions. Only this.

### 5. Assignment outcomes (instructor-reported data)
**What:** per-paragraph struggle signals, question answers (including which option was
  selected for recognition-level questions), confidence ratings (high/low), correctness,
  and self-reported substate (confusion/overload) when provided.
**Where stored:** `outcomes` table, Neon Postgres.
**Key privacy mechanism:** outcomes are stored under a PSEUDONYM — HMAC(assignment.salt,
  account_id) — not the real account ID. The pseudonym changes per assignment. A reader's
  behavior in one class cannot be linked to their behavior in another class, even by
  someone with full database access.
**Who can read it:** instructors can see aggregate outcomes for their class, subject to
  a 5-pseudonym cohort floor (if fewer than 5 distinct pseudonyms contributed to a
  figure, that figure is suppressed). No instructor can see individual student outcomes.
  In identified-mode classes (opt-in, chosen at class creation, permanent), instructors
  can see which student corresponds to which pseudonym — but only within that assignment.
**How long retained:** until the class or assignment is deleted.
**Used for:** aggregate comprehension reporting (trouble map, difficulty/misconception
  classification, instructor AI recommendations). Not used for anything outside the
  educational purpose of the assignment.

### 6. Scroll kinematics (baseline collection for anti-AI-scroll detection)
**What:** session-level statistics about how a reader scrolls — velocity distribution,
  jitter patterns, micro-correction rate, acceleration events, smooth scroll ratio.
  Eleven aggregate statistics per session. NOT raw event-by-event scroll data.
**Where stored:** `scroll_sessions` table, Neon Postgres.
**Key privacy mechanism:** stored under the same per-assignment pseudonym as outcomes.
  Not linked to account_id. Not linkable across assignments.
**Who can read it:** no one, yet. The table is write-only. No endpoint reads from it.
  Any future read path must apply the same 5-pseudonym cohort floor as outcomes.
**How long retained:** indefinitely, as a training corpus. The collection label
  ('baseline_v1') marks the period when all users were verifiably human.
**Used for:** building a classifier that distinguishes human from automated scroll
  behavior. This classifier will be used to detect sessions where a student used an
  automated tool to scroll through an assignment rather than reading it — protecting
  the integrity of instructor-facing aggregate data.
**Not used for:** identifying individual users, tracking reading behavior across
  sessions, or any purpose outside anti-gaming detection.
**When collection begins:** at launch, for all signed-in users in assignment context.
**Transparency:** users will be informed of this collection in the privacy policy.
  The mechanism and purpose will be described plainly.

### 7. Uploaded documents
**What:** PDFs and other document files uploaded by instructors for assignment.
**Where stored:** Cloudflare R2 (object storage), private bucket.
**Who can access it:** the instructor who uploaded it (via a signed URL), students
  in the assigned class (via a signed URL, for the duration of the assignment).
**How long retained:** until the assignment is deleted or the instructor deletes the
  document.
**Used for:** serving the document to students for assigned reading. Not processed for
  any other purpose beyond what the instructor explicitly assigns.

---

## What is NOT collected

These are explicit non-collection decisions, not defaults:

- **Browsing history.** alcoia does not read, store, or transmit the URLs a reader
  visits outside of what's needed to detect whether the extension should be active.
- **Page content.** The full text of a page is never sent to the server. Only the
  specific passage that triggered a struggle signal is sent, transiently, for question
  generation.
- **Individual reading session history.** A reader's comprehension history across
  sessions is not stored in a form that links their behavior over time to their identity.
- **Camera or eye tracking data.** alcoia has no camera access. It will never have
  camera access.
- **Keystroke or typing behavior.** The extension does not capture keystrokes.
- **Reading speed or time-on-page by URL.** The extension tracks reading pace against
  text difficulty within a session, but this data is not transmitted to the server for
  non-assignment reading.

---

## Where everything is physically stored

| Data | Location | Provider |
|---|---|---|
| Accounts, sessions, outcomes, scroll kinematics | PostgreSQL database | Neon (cloud-hosted Postgres) |
| Uploaded assignment documents | Object storage | Cloudflare R2 |
| AI question/explanation generation | API request | Groq (LLaMA models) |
| Email delivery (magic links) | Transactional email | Postmark |
| Billing and payment processing | Merchant of record | Creem.io |
| Server hosting | Node/Express application | Render |
| Marketing site, console | Static hosting | Cloudflare Pages |

---

## How the data could be exported

For anyone asking: yes, the Neon database can be exported as CSV or SQL dump via
Neon's dashboard or via standard PostgreSQL tooling. This is intentional — the data
belongs to the educational institutions and readers who generated it, and it must be
portable. A data export on account deletion request is a planned feature (see privacy
policy draft).

For research purposes (building the anti-AI-scroll classifier): the scroll_sessions
table can be exported as CSV, with the pseudonym column included. The pseudonym is not
reversible to an identity without the assignment salt and the HMAC key, so the exported
corpus is genuinely pseudonymized for classifier training purposes.

---

## What needs to be added to the privacy policy

The privacy policy draft already covers items 1–5 and 7 above. What it does not yet
cover, because it was built after the policy was drafted:

- **Item 6 (scroll kinematics):** needs a plain-language paragraph explaining that
  for signed-in users in assignment context, aggregate scroll statistics are collected
  to detect automated scrolling behavior, stored pseudonymously, and not used for
  individual profiling. This should go in the same section as the outcomes disclosure.

- **The anti-AI-scroll classifier intent:** users should know that this data will
  eventually be used to train a classifier, and what that classifier is for. Plain
  language: "We collect aggregate statistics about how you scroll during assigned
  readings to help us detect automated tools that might falsify comprehension data.
  This data is stored pseudonymously and is not linked to your identity across
  assignments."

---

## Open questions for the going-live checklist

1. **Legal review of scroll kinematics collection.** The privacy policy needs the
   updated section before launch. Does the lawyer who reviews the policy understand
   what a behavioral biometric is and why the pseudonym design matters here?

2. **Consent mechanism.** Currently, scroll kinematics collection is passive — it
   happens for all signed-in users in assignment context. Is passive collection
   sufficient under GDPR for EU users, or does this require an opt-in given its
   nature as a behavioral signal? This is a legal question, not a technical one.

3. **Retention policy.** The document says "indefinitely, as a training corpus."
   GDPR storage limitation (Art. 5(1)(e)) requires a defined retention period or a
   legitimate reason for indefinite retention. A classifier training corpus with a
   "baseline_v1" label is a legitimate research purpose — but the retention justification
   needs to be stated explicitly in the privacy policy.

4. **The longitudinal outcome store (DC-2a) has not been run yet.** Before building
   it, confirm with legal whether connecting outcomes across sessions under the same
   pseudonym-per-assignment architecture constitutes a form of longitudinal profiling
   that changes the GDPR lawful basis analysis. The pseudonym per assignment means the
   link is technically not reconstructible without the salt — but the intent of the
   data structure is longitudinal analysis, which is a different use case than per-
   assignment reporting.

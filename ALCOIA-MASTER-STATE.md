# alcoia — master state document

**Add to:** both repo roots, or a third private `alcoia-docs` repo if you want one canonical copy.
Whichever you choose, only one copy should be edited — a document like this drifting into two
different versions is worse than not having it.
  
**Purpose.** One place that says what exists, across every dev setup, in language a new person —
or a future you — can follow without having sat in on the decisions. Concepts are explained, not
assumed. Status is verified against the actual repos, not remembered.

**Last verified:** extension `main` at commit `d5ab2c1` (746 tests, 35 files, lint clean, 4
pre-existing warnings). Server state as reported after S9/S10; not independently re-run here —
flagged below as unverified where it matters.

---

## 1. The three repositories

| Repo | Visibility | What it is |
|---|---|---|
| `alcoia-group/alcoia` | **Public**, AGPL-3.0 | The browser extension. Runs on the reader's machine |
| `alcoia-group/alcoiaServer` | **Private** | The API: accounts, entitlements, seats, assignments, aggregation, the AI proxy |
| Admin console | **Private**, not yet started | The web app instructors and org admins use |

**Why the extension is public and the others are not.** The extension's privacy claim only means
something if it can be checked — it runs on the reader's device, so the code has to be readable to
be trustworthy. The server and console run on infrastructure nobody but you can inspect, so
publishing them buys no trust while giving away entitlement logic, aggregation logic, and
eventually SSO detail. Different objects, different reasons.

---

## 2. What alcoia is, for anyone arriving cold

A browser extension that notices when a reader is struggling with a page and asks them a
**retrieval question** about what they just read, instead of summarising it for them.

**The one idea everything else follows from:** alcoia's purpose is to make the reader need it
less. Declining intervention need is success, not daily engagement. This is why:

- A correct answer gets a tick and nothing else — no praise, no explanation. Explaining something
  the reader already got right adds load exactly when they're consolidating it, and trains them to
  wait for the system to do the closing work.
- Explanation is the **failure path**, reached only after a wrong answer.
- There is no XP, streak, leaderboard, or celebration animation of any kind. If a reward attaches to
  answering questions, and questions fire on detected struggle, a reader chasing the reward reads
  slowly and scrolls back to *look* struggling. That's real human scrolling, faked — nearly
  undetectable — and it poisons the exact data the detector learns from.

**How it decides when to interrupt.** Reading pace against text difficulty, compared to the
reader's own baseline (not a population average — a dyslexic reader compared to themselves isn't
flagged for reading slowly). Scroll regressions, re-reading, pauses. It gets one interruption per
three minutes, a budget that scales with how much has actually been read, and it backs off the
more a reader dismisses it. Never twice on the same paragraph, never on a state the system isn't
confident about.

**What it does not do.** No webcam, no eye tracking — that was tried, measured, and removed
entirely; browser signals turned out to be the honest primary sensor. No fingerprinting of any
kind. No record, anywhere on the server, of which pages a specific person read.

---

## 3. Concepts, explained once

### The install token

A free reader needs no account. On first use the extension asks the server for a random, opaque
token — not computed from anything about the device, just issued. Every AI call carries it. The
server counts calls against the token and stops at a ceiling; if the reader clears their storage
they simply get a new token, and that's accepted as the cost of not fingerprinting anyone. The
point of the token isn't to stop a determined individual — it's to stop the endpoint being found
and scripted by someone who never installs the extension at all.

### The span rule

The single rule that keeps question generation honest: **every question must quote, word for word,
a sentence that's actually in the passage.** If the model can't point at real text backing a
question, the question is thrown away rather than shown. This is what stops the model inventing a
question and asking it of someone who's already struggling — an invented question is worse than no
question.

### The pseudonym

How a lecturer can see "78% of the class struggled with paragraph four" without the server ever
knowing which specific student struggled. When a student joins an assignment, their outcomes are
filed under an ID computed from a secret that assignment alone has, combined with their account.
The same student across three sessions produces the *same* ID within that assignment — so the
percentage counts people, not visits — but a *different* ID under every other assignment's secret,
so nothing can be linked across assignments. There's no table mapping ID back to student; it's a
calculation, not a stored link. Delete the secret and the calculation becomes impossible forever,
while every percentage already computed stays exactly as it was.

### Reporting modes

Most classes are `anonymous` — the mode above. Some institutions need `identified`, per-student
visibility, and that's supported, but only under conditions that can't be loosened later: chosen
when the class is created, never switched afterward, and disclosed to students the moment they
join. The reason it can't flip later is straightforward — if you tell a student "aggregate only"
and then make it identified, the original promise was false the moment it was made, and it was
made to the student, not the school, so nothing the school signs afterward fixes that.

### LTI (Learning Tools Interoperability)

The standard that lets an outside tool plug into a school's learning platform — Canvas, Moodle,
Blackboard. In LTI's own language, the school's platform is the **Platform** and alcoia is the
**Tool**. Version 1.3, which is what's built, runs on OAuth 2.0 and OpenID Connect.

What it buys: a student clicks a reading inside Canvas and lands in alcoia already signed in,
already in the right class, already on the right document — no invite link, no separate signup.
Class rosters sync automatically from Canvas enrolment (this is called **NRPS** — Names and Role
Provisioning Services), so a student who drops the course loses access without anyone doing
anything by hand. An instructor can attach an alcoia reading to a Canvas assignment from inside
Canvas itself, called **deep linking**.

**What was deliberately left out: grade passback (AGS).** Every LMS integration is expected to
write scores back to the gradebook, and alcoia doesn't. If a grade depended on alcoia's numbers,
students would learn to read slowly and scroll back on purpose to look like they were struggling —
same corruption as the reward problem above, just arriving through the gradebook instead. The
honest sentence for a sales conversation is *we integrate with your gradebook; we don't grade your
students.*

### Assignment-scoped pseudonym vs. session-rotated — the alternative that was rejected

Worth recording precisely because it will look like the more private option to whoever reads this
next, and it isn't the one that was chosen. A pseudonym that changed every session would make
identification impossible in an even stronger sense — but it would also make the same student's
three visits count as three different people. That doesn't just blur the number, it biases it: the
students who come back most get counted the most, so a class average would end up looking better
than the actual understanding in the room. The chosen design accepts a bounded, structural
capability (see the pseudonym explanation above) specifically to avoid that bias while still
keeping identification impossible in ordinary use.

### The magic-link handoff

A paid account signs in by clicking a link in an email — no password. The awkward part: that link
opens in a normal browser tab, but the credential needs to end up inside the *extension's* private
storage, which a web page can't reach directly. The fix is a short-lived, single-use code: the
email link verifies and hands back a code good for about two minutes, and only the extension
exchanging that code gets the real, long-lasting sign-in. A stolen code is worthless almost
immediately; a stolen long-lived credential would not be.

---

## 4. Server — status

**Everything from S1 through S10 is built, merged, and reported as tested** (lint clean, 336
passing / 57 skipped as of the LTI review). This document does not re-verify server tests directly;
that access wasn't available in this session. Treat the two items below as the priority before
trusting any of it in production.

| # | What it does | Status |
|---|---|---|
| S1 | Schema, migrations, health check | ✅ |
| S2 | Install tokens, four-layer rate limiting | ✅ |
| S3 | Accounts, magic-link auth, entitlements | ✅ |
| S4 | Question generation and summaries, span rule enforced | ✅ |
| S5 | Orgs, classes, seats, invites | ✅ |
| S6 | Assignments, the pseudonym secret, document upload | ✅ |
| S7 | Outcome recording and class aggregation | ✅ |
| S8 | Receipt signing, verification, submission | ✅ |
| S9 | LTI 1.3 / Canvas | ✅ |
| S10 | Magic-link → extension-session handoff | ✅ |

### Before this is trusted with real money or real students

**The 57 skipped tests have still never run against a real database.** This includes the
concurrency test proving two simultaneous sign-ups can't oversell the same seat — exactly the kind
of bug that never shows up until the first time a class actually fills up in real time. Stand up a
real or containerised Postgres and run the full suite before anyone relies on seat capacity or the
reporting-mode lock holding under load.

**PPTX and DOCX documents upload but are never processed.** The server is honest about this — it
marks them `unsupported` rather than pretending — but it means an instructor can currently upload a
PowerPoint to an assignment and nothing downstream will work with it.

**LTI has three named simplifications**, all reasonable for now but worth remembering before
scaling: one Canvas connection assumed per school, class rosters read in a single page (a very
large class could be truncated), and no separate rate limiting on the LTI endpoints yet.

---

## 5. Extension — status

**39–44 are all built and verified**, 746 tests across 35 files, lint clean. That's the PDF viewer
matching the browser's own down to pixel density and highlight rendering, discoverable highlight
controls with a persistence toggle, the paid document library with its entitlement gate, and the
adaptive question engine — recognition through free recall, scenario and adversarial questions, all
within one reading session, no cross-session memory.

**What the extension cannot do yet, verified by checking the code rather than assuming:** there is
no sign-in screen, no way to accept a class invitation, no assignment notification, and nothing
that recognises an LTI launch. Every server capability above that needs a signed-in reader has
**no door into it from the extension.** That's not a bug — nobody asked for it yet — but it's the
actual gap, and it's larger than a single item.

---

## 6. What's left — grouped by what each piece unlocks

### Group A — makes an account usable at all

Nothing in group B or C can be used by a real reader until this exists.

- **Sign-in UI.** Enter an email, receive the magic link, land back in the extension signed in via
  the S10 handoff. This is the front door for every paid feature.
- **Account state in settings.** Which plan, whether signed in, a sign-out control.

### Group B — makes seats and classes usable

- **Accept an invitation.** A student clicks a link or enters a class code and joins.
- **Assignment notice.** A badge and a popup entry when a reading is assigned — never a card
  dropped over whatever the reader happens to be reading at that moment.
- **The disclosure screen.** The single most important sentence in the institutional product,
  shown the moment a student joins a class: the instructor sees aggregate results only, nothing
  individual, unless the student chooses to submit a receipt. This has to exist before Group B
  ships at all, not as a follow-up.
- **Leaving a class.** A visible way to leave and understand what that does to their access.

### Group C — LTI-specific

- **Recognise an LTI launch** and show the same disclosure screen automatically, without a manual
  join step, since Canvas already knows who the student is.
- **Nothing else.** All the OIDC, roster-sync and deep-linking work is server-side and already
  done; the extension's whole job here is showing one screen at the right moment.

### Group D — content

- **DOCX handling.** No extraction exists at all yet, on either side. `.docx` is a zip file full of
  XML underneath, and the extension already carries the library needed to read zip files for the
  PowerPoint path, so this is real but not exotic work.
- **PPTX processing server-side**, to match what the extension can already open.

### Group E — quality, not new capability

- **Run the 57 skipped server tests against a real database.** Correctness, not a feature.
- **Question quality has never been read by a human at scale.** The mechanism is sound; nobody has
  sat down and judged twenty real questions for whether they're actually good.

**Recommended order: A, then B (with the disclosure screen first inside it), then C, then D, with
E's database check happening before any of A-C goes in front of a real class.** C only takes an
afternoon once B exists, because the hard part is already built. D can happen any time — it doesn't
block anything else and nothing is currently waiting on it.

---

## 7. Decisions that keep coming up — recorded once so they stay decided

| Decision | Reason |
|---|---|
| No account required for the free tier | Removes the biggest reason people don't try something |
| No fingerprinting of any kind | A reader who clears their data deliberately did that on purpose; defeating it is exactly the covert observation this product is not allowed to do |
| No accuracy percentage, anywhere, ever | Every figure that has existed across this project came from synthetic test data, and one of them has a known measurement flaw. None have ever been checked against a real reader |
| No reward for answering | Turns the detector's own training data into something readers can fake |
| No grade passback to any LMS | Same corruption, arriving through the gradebook |
| The server never stores which pages a person read | The one sentence the whole privacy story rests on |
| A class's reporting mode is locked at creation | A promise of anonymity that changes later was false the moment it was made |

---

## 8. If you only remember four things from this document

1. **The extension and the server are both mostly finished. Almost nothing connects a real person
   to either of them yet.** That's the actual next phase — not new capability, just a door in.
2. **The disclosure screen is not a nice-to-have inside Group B — it has to exist before Group B
   ships at all.** A student assigned a reading before they've been told what their instructor can
   see is the one mistake in this whole plan that can't be undone after the fact.
3. **The 57 skipped server tests are the single biggest unknown in the project right now.** They
   include the test proving seats can't be oversold under real concurrent load, and nobody has
   watched it actually pass.
4. **DOCX doesn't exist on either side.** It's been assumed available in conversation more than
   once; it isn't yet, on either end.

# alcoia — Future Implementations

Decisions made, rationale recorded. Every item here was kept deliberately.
Items declined: study sheet creation (chews for the student, defeats the product's purpose).

---

## Domain structure (decided)

Before anything else — the subdomain map, since it affects how features are described below.

| Subdomain | Purpose | Status |
|---|---|---|
| `alcoia.app` | Marketing site | Live |
| `console.alcoia.app` | Instructor console | Live |
| `server.alcoia.app` | Internal server proxy to Render (Worker) | Live |
| `api.alcoia.app` | Future public API — RESERVED | Reserved, do not use |
| `developers.alcoia.app` | API documentation console | Build now |
| `status.alcoia.app` | Status page | Future |
| `dev.alcoia.app` | Development environment | Future |
| `staging.alcoia.app` | Staging environment | Future |

**When Render is upgraded to paid tier:** move `server.alcoia.app` from the Cloudflare
Worker proxy to a real Render custom domain. Delete the Worker. Keep the keep-alive cron
job. See decisions-and-principles.md for the full migration steps.

---

## 1. Anti-AI scroll detection

**What it is:** A classifier that detects automated or bot-generated scroll behavior,
distinguishing it from genuine human reading patterns. Protects the integrity of
instructor-facing aggregate data.

**Why the timing is critical:** The baseline can only be established while all users are
human. Once automated scroll tools become common, the signal is permanently contaminated.

**What to collect in v1:** Raw scroll kinematics per session — velocity variance,
jitter patterns, micro-correction frequency, acceleration events. Stored as a session-level
JSON blob under the same per-assignment pseudonym as outcomes. Already built (DC-1a, DC-1b).

**Classifier:** post-launch, after 6 months of real user sessions.

---

## 2. Video support

**What it is:** Detection applied to video — replay loops, scrubbing, pausing —
generating retrieval questions anchored to transcript segments.

**Hard prerequisite:** transcripts. Without a transcript, there is no "passage" to anchor
a question to. YouTube CC, uploaded transcripts, auto-generated captions are the three
practical sources.

**What to collect in v1:** nothing — video support needs its own architecture.

**Status:** Future, after teach mode is shipped.

---

## 3. Teach mode

**What it is:** The reader explains a concept to alcoia, which plays a confused student
— "why does that follow?", "can you give an example?". The reader produces knowledge;
the AI is the audience, not the source.

**Why it's the most aligned future feature:** teaching forces knowledge organization.
The Feynman technique is one of the most validated active-recall strategies in the
literature. This is the purest expression of "AI that makes you need AI less."

**Infrastructure:** the free-text answer and grading machinery from the adversarial
question level already exists. Teach mode extends it — the AI evaluates explanation
quality rather than factual correctness.

**Status:** Near-future. Build immediately after the extension is stable.

---

## 4. Prediction mode (pre-reading commitment)

**What it is:** Before a key reveal ("the researchers concluded that..."), alcoia
surfaces a prompt: what do you think comes next? The reader commits, then reads. The
mismatch between prediction and reality drives stronger memory encoding.

**What's already built:** the prequestion mechanic from item 13e — discourse-marker
detection and occlusion-then-reveal for single sentences. What's not built: free-text
prediction commitment before a longer argument.

**What to collect in v1:** prediction accuracy data — when a prediction is made and the
reader reads the reveal, was their prediction close? Feeds the personalization layer.

**Status:** Near-term extension of existing mechanism.

---

## 5. Correct answer silence

**What it is:** A design principle. Correct answers get a brief confirmation (✓), nothing
more. No explanation, no praise. The consolidation moment belongs to the reader.

**Current status:** Built and verified by code audit. Explanation-on-failure fires
correctly. Correct-answer silence confirmed intact. No regression found.

**Action:** Regression test added (tests/question-card.test.js). No further work needed.

---

## 6. Independence trajectory metric

**What it is:** A long-term metric tracking AI-assisted vs. independent retrieval over
time per user. "Month 1: 47% of passages needed a question prompt. Month 6: 14%."

**Why this matters:** Every other AI company measures success by engagement — more usage.
alcoia can measure success by declining engagement. "You're relying on me less. That's
the point."

**On pricing:** do not adapt price to independence level. The commercial incentive would
be to keep users dependent. The trajectory is a quality signal and marketing story,
not a pricing mechanism.

**What to collect in v1:** the data already exists (assist counts, confidence, correctness).
What's missing is a longitudinal store connecting outcomes across sessions — DC-2a,
currently deferred for privacy reasons.

**Status:** Build the longitudinal store when consent architecture is resolved. Surface
metric post-launch once 30-day minimum data exists per user.

---

## 7. Personalized learning model

**What it is:** Per-user modeling of cognitive behavior — which conditions produce durable
retention for this specific reader, where confidence diverges from accuracy, which concept
types they learn quickly.

**Distinct from:** surface preferences ("you like dark mode"). This is about cognitive style.

**What to collect in v1:** see DC-2a. The key signal is the confidence-accuracy divergence
over time, per passage type and difficulty level.

**Status:** Long-term. Model requires 6+ months of real user data. Blocked on DC-2a consent
resolution.

---

## 8. Differential intervention by cognitive state

**What it is:** The current system detects confusion, overload, and disengagement (items
13a–13d) but routes all three to the same retrieval question. The research is clear that
each state needs a different response.

**What the research says:**

*Confusion* — keep the retrieval question but use Socratic framing ("why does this follow?")
not factual recall. Do not explain early. The reader needs to resolve the confusion, not
have it resolved for them.

*Overload* — do NOT ask a hard retrieval question. Reduce load: re-read prompt, worked
example pointer, or a pause. Asking a hard question when working memory is full makes
things worse.

*Disengagement* — re-engage with surprise: a prediction challenge, a counterintuitive
question. A comprehension question the reader will dismiss is the wrong tool.

**What's confirmed about the current ladder (from real code audit):**
`nextLevel()` in epistemic-engine.js does not escalate on failure — recognition and free_recall
both hold steady on a wrong answer. Only `scenario` steps down on a miss. The real gap:
no floor exists below `recognition` — a reader who keeps failing recognition just keeps
getting more recognition questions, never a gentler re-encoding prompt.

**Status:** Post-launch, high priority. When built, also adds the floor below recognition
and extends to substate-specific follow-up on failure (item 12 below).

---

## 9. Reader-facing calibration surface

**What it is:** Showing readers their own confidence-accuracy pattern — specifically where
they consistently feel they understand something and then don't.

**The fluency illusion:** the current confident-wrong signal reaches the instructor. The
reader themselves never sees it. "You were confident but incorrect here" shown to the
reader is potentially the most valuable metacognitive feedback available.

**Blocked on:** DC-2a (longitudinal store, requires consent architecture resolution).

**Why this reframes DC-2a:** the consent ask becomes "let us track your confidence-accuracy
pattern across sessions, and we'll show you where your understanding is weaker than it
feels." That's a fair exchange, not bureaucratic data collection.

**Status:** Blocked on DC-2a consent resolution.

---

## 10. Time-budget selector

**What it is:** A reader choice — not a different mechanism, just different pacing and
honest framing. "Studying over time" uses full material with spaced, varied retrieval.
"Exam is soon" triages to a condensed, prioritized slice with rapid retrieval-with-feedback.

**Important:** this is NOT a summarization feature. Both modes still ask — the reader still
does the cognitive work. What changes is how much material is covered and how honestly the
tradeoff is stated.

**What must be stated honestly in the "exam soon" mode:** "This helps you perform well
tomorrow. For material you want to actually keep, use the full mode." Research (Kornell
2009): 72% of people who benefited more from spaced practice still believed massed practice
worked better. This is why the honest framing is essential, not optional.

**Status:** Near-term, after dynamic retrieval and feedback/routing fixes are solid.

---

## 11. Dynamic retrieval — question type rotation per concept

**What it is:** The same underlying concept asked from a different angle each time it
resurfaces — definition → explanation → prediction → comparison → application → critique →
integration. Never the same question twice.

**Why this is the most important lever toward genuine understanding:** varied retrieval
beats repeated identical retrieval for transfer. Repeating an identical question teaches
memorization of that specific phrasing, not the underlying knowledge structure.

**The real blocker:** only recall and inference exist as real question types server-side.
The five other types (prediction, comparison, application, critique, integration) were
rejected by the span-grounding rule — they can't be verified against a verbatim quote
from the passage. This is CLAUDE.md open question #1, still open.

**Options to resolve the blocker:**
- Relax the span rule for higher-level questions with a different safeguard
- Accept a narrower taxonomy rotating within recall and inference only
- Gate higher-level questions behind instructor/human review

**Status:** Highest priority open architectural question. Resolve deliberately, not by
default inertia.

---

## 12. Substate-specific follow-up on failure

**What it is:** When a reader gets a question wrong, the explanation that fires is
currently generic with respect to substate. An overloaded reader and a confused reader
receive the same response.

*Overload follow-up:* break the explanation into smaller pieces, point at the single
narrowest sub-concept, strip extraneous detail.

*Confusion follow-up:* Socratic — point at the specific gap in reasoning, let the reader
close it where possible, use the explanation as a scaffold not a replacement.

**Connection to item 8:** this is the same underlying mechanism (read substate, branch
response) applied at the follow-up moment rather than the initial intervention moment.
Build both together.

**Status:** Same as item 8 — post-launch, high priority.

---

## 13. alcoia API product

**What it is:** A commercial API layer exposing alcoia's reading intelligence to
developers building EdTech platforms, AI study tools, PDF readers, corporate training
systems, and knowledge management tools.

**Strategic framing:** the consumer SaaS product is the proving ground. Real users → real
behavioral data → better intelligence → an API competitors cannot replicate without that
data. The SaaS and API reinforce each other.

**The five planned API products:**

*Reading Intelligence API* — the most differentiated. Takes session behavioral signals
and returns a classified reading state with confidence and evidence. States: `on_pace`,
`skimming`, `struggling`, `drifting`, `absent`, `unknown`. Substates (when struggling):
`confusion`, `overload`, `unclear`. Returns evidence alongside every classification —
not a black box assertion about mental state.

*Retrieval API* — generate span-grounded retrieval questions at specified difficulty levels,
evaluate answers. The span-grounding rule is the key differentiator from generic LLM
question generation.

*Intervention API* — given a reading state, budget, and context, recommend whether and
how to intervene. Advisory only — the developer decides what to surface.

*Explanation API* — explain a concept, passage, or wrong answer, adapted to context.
Only fires after a failed retrieval attempt.

*Reading Receipt API* — a cryptographically signed record of reading interaction.
CRITICAL INVARIANT: a receipt is evidence of reading interaction, NOT proof of
comprehension. This distinction must appear prominently in all documentation and must
never be softened or implied otherwise. Planned for later.

**Planned pricing:**
- Developer (free): 1,000 calls/month
- Developer paid: $29–49/month, 50,000 calls/month
- Growth: $149–299/month, 500,000 calls/month
- Enterprise: custom

Reading Intelligence is signal-only (no AI cost). Retrieval and Intervention call LLM
inference — usage-based AI costs apply. This distinction must be clear in pricing docs.

**What exists now:**
- `alcoia-api-console` repo brief written (ALCOIA-API-CONSOLE-BRIEF.md in /home/claude/final/)
- `developers.alcoia.app` subdomain to be set up in Cloudflare
- Documentation-only site with planned API shapes and status badges to be built

**Status:** Documentation site (developers.alcoia.app) — build now.
Actual API endpoints — after the consumer product has real user data and the
intelligence is proven in production.

---

## 14. Status page

**What it is:** A real-time page showing the health of alcoia's infrastructure components —
API server, AI question generation, email delivery, file storage. Similar to groqstatus.com.

**Why:** users hitting issues (magic link not arriving, questions not generating) currently
have no way to know if it's a global problem or something on their end. A status page
converts "is this broken?" from a support ticket into a self-service answer.

**Implementation:** do not build from scratch. Use Instatus, Betterstack, or Upptime
(GitHub-hosted, free). Connect to the existing `/health` endpoint on the server. Add a
separate AI-health check endpoint that actually calls question generation and verifies a
response — a status page that only checks "is the server up" but not "can it generate a
question" shows green while the most important thing is broken.

**Subdomain:** `status.alcoia.app`

**Status:** Future, during or after beta, before wide promotion.

---

## 15. Sticky notes on highlights

**What it is:** When a reader highlights a passage, they can attach a sticky note alongside
the highlight text. How this interacts with the existing notes/highlights architecture
needs design thought before building.

**Status:** Rough idea, needs refinement. Post-beta.

---

## 16. Notification dot and multi-class joining (post-beta extension features)

**Notification dot:** a badge on the extension icon when a new assignment is available.

**Multi-class joining:** students can belong to more than one class simultaneously —
important for college students taking multiple courses.

**Status:** Neither blocks beta launch. Post-beta.

---

## 15. For educators page — redesign as a product landing page

**What it is:** The current `/for-educators` page is a thin positioning section
on the marketing site. As the instructor product matures (console, aggregate
dashboard, AI recommendations), the page should function as a proper product
landing page with its own hero, feature explanation, and CTA.

**What the redesigned page should include:**
- A hero with a clear product statement: "Know where your students actually
  got lost — not just whether they finished reading."
- The aggregate dashboard explained visually: trouble map, confident-wrong
  flag, what AI recommendations look like in practice
- Privacy model explained plainly: "You see patterns, not individuals"
- How fast setup is: create a class, upload a document, invite students —
  minutes, not hours
- Direct pricing link to Teams/Classrooms tier
- CTA straight to /start-pilot

**When to build it:** after the console's aggregate view has been tested with
real data and the AI recommendations are confirmed working, so screenshots
and descriptions are honest. Not before.

**Status:** Future, post-beta.

---

## 16. alcoia intelligence — the self-improving platform layer

**What it is:** not a feature but a long-term architectural vision. alcoia
intelligence is a continuously improving model of how humans actually learn
from text — what produces durable understanding, what produces the illusion
of understanding, where the gap between felt comprehension and actual
retrievability appears, and how to close it efficiently.

**The mechanism is a feedback loop, not autonomous self-improvement:**
real usage → validated intervention outcomes → improved difficulty and
intervention models → better recommendations for every product built on
the platform.

**The goal stated plainly:** every person who uses alcoia (or any product
built on it) becomes measurably better at retaining and using what they read.
Not because alcoia is always there — but because it has improved the
underlying cognitive habits that produce understanding. The system succeeds
by making itself less necessary.

**The "level up" metaphor explained properly:** as alcoia intelligence improves,
it finds comprehension gaps that earlier versions couldn't detect. A reader
who has cleared the gaps that level 15 could find still has gaps that level 16
can detect. The intelligence improves; the reader's mastery grows with it.

**What NOT to build yet:** a training pipeline. Before 50,000+ real user
sessions with outcome data, there isn't enough data to improve models
meaningfully. The investment is premature. Collect the data first (DC-1a,
DC-1b already in place), validate the flywheel internally, then build the
training infrastructure when the data justifies it.

**Relationship to the developer platform:** the API gets better as the consumer
product generates more validated outcome data. The consumer product gets better
as developers expose alcoia intelligence to more diverse learner populations.
The flywheel is real and the two products reinforce each other — this is the
moat argument, and it's correct.

**Status:** Long-term architectural direction. The data collection infrastructure
is already in place (DC-1a, DC-1b). Training pipeline: after 50,000+ real
sessions. Developer platform exposure: after first API customers.



---

## 17. On-demand equation and figure explanation

**What it is:** When a reader selects mathematical notation, a figure reference,
or a technical term they don't recognize, alcoia offers an on-demand explanation
grounded in the surrounding passage context. This is targeted scaffolding for
genuine prerequisite gaps — not a summary of the passage, but a specific bridge
for content the reader couldn't encode in the first place.

**Built:** the client-side feature is complete (selection-explain.js, tooltip
UI, explanation panel, session dedup, outcome flag). Two server-side gaps remain:

- `explain_equation` mode missing from `summary-prompts.js` — mathematical
  notation currently uses `explain_more` as an interim stand-in. A dedicated
  mode with a math-aware prompt is needed for real quality.
- `explanation_preceded_attempt` field not yet stored server-side — the outcomes
  route needs the field added to its allowlist and the DB schema needs a column.
  Client already sends it; server silently ignores it.

**The ordering principle:** explanation first, retrieval after. This is the
inverse of the standard flow and deliberate — you cannot retrieve what was never
encoded. The explanation delivers the prerequisite; retrieval consolidates it.

**The architectural gap:** the prerequisite-gap flag only matters inside the PDF
assignment context, where `submitOutcome` fires. `selection-explain.js` runs on
ordinary web pages where there's currently no assignment context. The wiring will
connect automatically when the PDF viewer gets a selection surface (future item).

**Status:** client-side built. Two server-side additions needed before the data
collection is functional. Add to a server prompt alongside the going-live work.

---

## 18. Prerequisite gap detection and the understanding vs. retention model

**The core insight, stated precisely:**

alcoia is currently built for readers who are consolidating understanding —
the testing effect is maximally applicable when the reader has already encoded
the material, even imperfectly. But most real readers come to a document to
*build* understanding, not to consolidate it. These are different cognitive
tasks requiring different interventions.

The right long-term solution: a model that routes interventions based on whether
the reader is encoding new content or consolidating familiar content. Not a
mode the reader chooses (learners are notoriously poor at diagnosing their own
needs — the fluency illusion is evidence of exactly this), but a signal the
system detects and responds to.

**The distinction in practice:**

*Consolidation context:* the reader has the prerequisite knowledge. They read
the passage, felt like they understood it (possibly a fluency illusion), and
alcoia's retrieval question surfaces whether the encoding was real. The testing
effect applies. This is alcoia's current primary use case.

*Encoding context:* the reader genuinely lacks prerequisite knowledge for this
passage. A retrieval question produces failure not because of a fluency illusion
but because there was nothing to retrieve. The correct intervention is scaffolding
(explanation, worked example, prerequisite definition) before any retrieval.

**The signal that distinguishes them (to be built):**

The `explanation_preceded_attempt` flag (already being collected at the client
level in item 17) is the first piece. When combined with: passage difficulty
score, reading pace ratio, confidence at answer time, and whether the reader
requested on-demand explanation — a sufficiently large corpus can train a
classifier that distinguishes these two states with reasonable accuracy.

The classifier does not need to be perfect. A prior probability based on passage
difficulty and the reader's prior performance on similar content is already useful.
The goal is not certainty but better-than-random routing of the intervention type.

**What to collect in v1:**
- `explanation_preceded_attempt` boolean per outcome (wiring built, server-side
  storage pending — see item 17)
- On-demand explanation request events per passage (which passages triggered
  the tooltip, which triggered a full explanation fetch) — these need a new
  server endpoint or be added to the existing events/telemetry infrastructure
- Outcome quality after explanation-preceded attempts vs. unpreceded attempts —
  the comparison is the training signal

**What NOT to build yet:** the classifier itself. The data doesn't exist at
scale. Build the collection infrastructure now; train the model after 50,000+
sessions with diverse content types.

**Status:** data collection infrastructure in progress (item 17 covers the
outcome flag). Explanation event logging needs a server endpoint. Classifier:
post-launch, after sufficient data.

---

## 19. L2 reading support (language learning)

**What it is:** alcoia's retrieval mechanism is structurally well-suited for
second-language reading acquisition. L2 reading comprehension is precisely the
domain where prerequisite knowledge (vocabulary, grammatical constructions) is
systematically absent, where scaffolding must precede retrieval, and where
spaced retrieval of vocabulary items is highly effective per the research.

**Why this is a distinct mode, not a feature addition:**

In L2 reading, the comprehension failure pattern is different from L1 reading:
- Vocabulary gaps are frequent, predictable, and addressable with targeted
  definitions
- The reader often knows the concept but not the word — so the intervention
  is a vocabulary bridge, not a worked example or Socratic question
- Spaced retrieval of the vocabulary items (not the concepts) is highly
  effective for acquisition
- The difficulty signal needs calibration: a high-difficulty passage for an
  advanced L1 reader may be low-difficulty for an intermediate L2 reader of the
  same language, or vice versa

**What L2 detection would look like:**

A reader in L2 mode shows: frequent selection of individual words (not equations
or figure references), high lexical rarity scores concentrated on specific word
types (content words, not function words), and retrieval failures that pattern
on vocabulary items specifically. This is a distinct behavioral signature from
L1 comprehension failure.

**What to collect now:** nothing specific — L2 detection requires the on-demand
explanation infrastructure (item 17) and the prerequisite gap detection (item 18)
to be built first, since L2 reading is a subset of the encoding context case.

**Status:** future, after items 17 and 18 are built and producing real data.
Do not scope separately until the prerequisite gap infrastructure exists.

---

## 20. Dynamic highlighting (clarification of what's already built)

**What exists:** the extension has a highlight feature that is user-initiated and
selection-based — not automatic. The reader selects text and it is saved as a
highlight. This is deliberately different from Moonlight AI's auto-highlight,
which selects key passages automatically (an AI-judgment substitution for the
reader's own attention).

**The design principle:** alcoia's highlights record what the *reader* found
significant. Auto-highlighting records what the *AI* found significant. These
are different signals, and the latter substitutes AI judgment for the reader's
own, which is exactly the pattern alcoia is built to avoid.

**What to not build:** auto-highlighting. Never. The moment alcoia tells a reader
what to pay attention to, it begins making the attention decision for them.

**What might be worth building (future):** a "flagged for review" state on
highlights — when a reader highlights a passage and later answers a retrieval
question on it incorrectly, the highlight could be marked visually to indicate
"you thought this was important and you didn't retain it." That's a metacognitive
feedback signal, not auto-highlighting. But this requires the longitudinal store
(DC-2a) to be in place first.

**Status:** existing feature is correct as built. Future enhancement blocked on
DC-2a consent resolution.

---

## 21. Medical education as a priority domain

**Why it matters more than general education:** the stakes of confident-wrong are
not academic failure but patient harm. A medical student who used AI summaries to
pass pharmacology exams has less retrievable knowledge than one who struggled through
retrieval practice on the same material. The research on this is unambiguous, and
the consequences are not.

**Where alcoia already fits without modification:**
- Pharmacology: dense text, requires genuine comprehension, drug interactions are
  exactly the confident-wrong failure mode (knowing the drug name is not knowing
  the mechanism)
- Pathophysiology: mechanism understanding, not fact recall
- Clinical guidelines: retrieving the right protocol under pressure requires genuine
  encoding, not recognition from a summary

**The instructor aggregate view in medical school context:** "11 of 28 students were
confidently wrong about this drug interaction" is exactly the signal a pharmacology
faculty member needs before a cohort enters clinical rotations.

**Where additional work is needed:**
Clinical reasoning is case-based and procedural — "A 67-year-old presents with..."
is a different cognitive structure than reading a paper. This maps to the
scenario/adversarial question types (item 11, dynamic retrieval) applied to clinical
cases rather than passages. The detection mechanism is the same; the question
generation needs case-specific framing.

**The argument for alcoia over Anki in medical education:**
Anki requires students to self-identify gaps and schedule their own review. Students
don't know what they don't know. alcoia catches the gaps they didn't know to look
for — behavioral detection of struggle, not self-reported confidence.

**Status:** future domain direction. No additional build work needed beyond what
items 8, 11, and 18 already describe. The existing product works for pharmacology
and pathophysiology text today. Clinical case reasoning requires item 11 (dynamic
retrieval with scenario-level questions) to be fully built first.



| Item | Collect in v1 | Build later |
|---|---|---|
| Anti-AI scroll | Raw scroll kinematics per session (already built) | Classifier (6 months post-launch) |
| Video support | Nothing | After teach mode ships |
| Teach mode | Nothing (infrastructure exists) | Immediately post-launch |
| Prediction mode | Prediction accuracy data | Near-term after teach mode |
| Correct answer silence | Already verified | Regression test added |
| Independence trajectory | Nothing new | Longitudinal store in v1, metric post-launch |
| Personalization | Longitudinal outcome store (DC-2a, blocked) | Model (6+ months post-launch) |
| Differential intervention | Nothing new (substate already collected) | Post-launch, high priority |
| Reader calibration surface | Nothing new (confidence data collected) | Blocked on DC-2a consent |
| Time-budget selector | Nothing new | Near-term, after items 11/12 |
| Dynamic retrieval | Nothing new | Blocked on span-rule decision — highest priority |
| Substate follow-up | Nothing new | Post-launch, build with item 8 |
| alcoia API product | Nothing | Documentation site now, API after real user data |
| Status page | Nothing | During/after beta |
| Sticky notes | Nothing | Post-beta, design first |
| Medical education domain | Nothing | Clinical case questions need item 11 first |

| For educators redesign | Nothing | Post-beta, after aggregate view tested with real data |
| alcoia intelligence flywheel | Nothing | Training pipeline after 50,000+ sessions |
| On-demand equation/figure explanation | explanation_preceded_attempt flag (built), explanation event logging (pending server endpoint) | explain_equation server mode, outcome schema update |
| Prerequisite gap detection + understanding vs. retention model | explanation_preceded_attempt + explanation events | Classifier after 50,000+ sessions |
| L2 reading support | Nothing | After items 17 and 18 produce real data |
| Dynamic highlighting — clarification | Nothing (existing feature is correct) | Metacognitive flag on highlights (blocked on DC-2a) |


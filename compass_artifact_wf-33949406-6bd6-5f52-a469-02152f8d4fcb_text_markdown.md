# alcoia Website Audit: De-AI-ify, Simplify, and Make It Engaging

## TL;DR
- **The copy is intelligent but unmistakably "AI-shaped."** Its single loudest tell is the compulsive contrast construction ("X, not Y" / "rather than" / "instead of"), which appears at least nine times on the homepage alone — the exact tic Wikipedia's "Signs of AI writing" lists as a primary detection signal and that a Washington Post analysis of 328,744 ChatGPT messages found the model "leans heavily on." Fix the contrasts, break the rule-of-three rhythm, and drop the "honestly labelled"/"Privacy, explained" header mannerism, and the site will instantly read more human.
- **The hero buries its CTAs and the site reads like a research abstract, not a product page.** Excessive vertical spacing between the nav and headline, plus a stacked hero, risks pushing "Add to Chrome" toward or below the fold on a 1366×768 laptop; the copy leans on cognitive-science vocabulary ("working memory," "d = 0.47," "retrieval practice") and over-explains itself against ChatGPT instead of telling a normal student or instructor what they actually get.
- **There are real unfinished-site defects that undermine trust:** literal `[ TODO: street address ]` placeholders in the footer, an unclosed parenthesis and a duplicated sentence in the Open-source section, a `.workers.dev` preview URL serving content canonicalized to `alcoia.com`, and a ✓-checkmark privacy list that reads as a 2026 "AI slop" tell. Fix these before any launch.

---

## Key Findings

1. **Contrast-construction overload is the dominant AI tell.** The homepage runs the "not X / rather than / instead of" antithesis at least nine times. Used once it's sharp; used every few sentences it advertises machine authorship. This is the highest-impact, lowest-effort fix on the whole site.
2. **Two header mannerisms ("Three honestly labelled stages," "Privacy, explained") signal AI-era copy** and should be replaced with plain, specific headers.
3. **The site is too technical for its stated audiences.** It explains itself in the language of a reading-science paper and defines itself by negation against ChatGPT rather than by what a student or instructor actually gets.
4. **The hero wastes vertical space and risks pushing the primary CTA below the fold** on standard laptops.
5. **Concrete unfinished-content defects** (TODO address, broken parenthesis, duplicate sentence) make the site look like a work-in-progress.
6. **Visual language leans on 2026 "AI slop" tells** — checkmark lists, three-card grids, three-stage rows — that a discerning viewer now reads as machine-generated.
7. **The student and instructor value props are present but thin and unequal**; the instructor story is compressed into a single sentence on the homepage.

---

## Details

### PART 1 — AI-writing patterns in the actual copy

> **Scope note:** I fully audited the **homepage**. The subpages (/how-it-works, /for-educators, /pricing, /privacy, /legal/*, etc.) could not be retrieved — the site is an unindexed Cloudflare Worker, and both the fetch tool and a dedicated subagent were blocked from loading them. The homepage embeds condensed versions of every section, so the patterns below are representative, but the subpages should be re-audited directly against this same checklist. Given the density of tells on the homepage, expect the same patterns amplified on the long-form pages.

#### (a) The "X, not Y" / "rather than" / "instead of" construction — PERVASIVE
This is the site's signature problem. Verbatim instances on the homepage:
1. *"being asked to recall something, **rather than** being shown it again"* (demo copy)
2. *"That is the whole mechanism. **Not a summary. Not a highlight.**"* (demo)
3. *"Being asked to recall something **instead of** re-reading it"* (quiz option)
4. *"That's what moves something into memory you can use later, **not** re-reading or highlighting it."* (quiz feedback)
5. *"it asks one short question about that exact sentence, **not a topic or summary**"* ("What happens instead")
6. *"Built on other people's evidence, **not ours yet**"* (Research H2)
7. *"uses retrieval **rather than** another summary to make the information stick"* (Versus AI shortcuts)
8. *"It's **not** a faster way to skim. It's a better way to actually learn."* (Versus AI shortcuts)
9. *"A way to actually keep what you read **instead of** skimming it once"* (Students card)

Why it matters: The "it's not X, it's Y" antithesis is now the most widely recognized signature of AI text. Wikipedia's "Signs of AI writing" lists negative parallelism ("It's not X, it's Y") as a primary detection signal, and a Washington Post analysis of 328,744 ChatGPT messages found the model "leans heavily on… negative parallelism: 'It's not X, it's Y;' or 'It's less about X and more about Y.'" Overused, the construction now signals a machine rather than emphasis.

**Rewrites (keep one, at most two, contrasts on the whole page):**
- Instead of *"Not a summary. Not a highlight. A short question…"* → **"It just asks you a short question about the sentence you slowed down on, and you answer in your own words."**
- Instead of *"It's not a faster way to skim. It's a better way to actually learn."* → **"It won't help you skim faster. It helps you remember what you actually read."** (one contrast, used deliberately, is fine)
- Instead of *"Built on other people's evidence, not ours yet"* → **"The science behind this is solid. Our own results aren't in yet — here's what we're standing on."**

#### (b) "[Noun], explained" / "[Noun], honestly" headers
- *"Three honestly labelled stages"* (How-it-works H2). Rewrite → **"How it actually works, in three steps."**
- *"Privacy, explained"* (footer label for /privacy). Rewrite → **"What we do with your data"** or **"Your data, plainly."**
- Watch for more of these on subpages (e.g. "Limits, explained").

#### (c) Em dashes as a stylistic device
The homepage is relatively restrained on em dashes (the arrows "→" are navigational, not dashes). This is a genuine strength — Wikipedia's guide calls em-dash overkill "probably the most infamous tell" ("Deployed for punchy emphasis where a comma would do—like this"). The bigger rhythm problem here is comma-spliced asides that do the same job as an em-dash aside, e.g. *"said here at the same size as everything above it, not in a footnote."* Keep watching the subpages.

#### (d) Rule-of-three constructions — frequent
- *"Nothing was actually read, and nothing sticks. There was never a moment that required recall."* preceded by *"The page is long… gives back three paragraphs. The three paragraphs feel like understanding."*
- *"Pace against the difficulty of the paragraph, against your own usual rate, scrolling back to re-read, selecting or copying, leaving the tab."*
- Three-item feature cards (Students / Educators / Language learners); three "stages"; three pricing tiers.
- Why it matters: Wikipedia's guide notes "AI defaults to triplets when listing anything: adjectives, benefits, takeaways." Rewrite guidance: vary list lengths — use two items, or four, sometimes. Break the triplet cadence.

#### (e) Negative parallelism ("It's not just X, it's Y")
Covered in (a); the "not X, it's Y" family is the specific sub-pattern Wikipedia calls out. The homepage's *"It's not a faster way to skim. It's a better way to actually learn."* is the textbook example.

#### (f) Inflated language / vague attribution / -ing analyses
- Good news: the Research section is *unusually* honest ("No accuracy figure for alcoia itself is published… a number with nothing real behind it is worse than no number at all"). Keep this — it's the most human, most trustworthy passage on the site.
- Watch item: *"Reading researchers call the fix retrieval practice"* is a mild vague-attribution. It's acceptable because specific citations (Rayner 1998; Just & Carpenter 1980; D'Mello 2016) follow, but soften to **"Psychologists have a name for the fix: retrieval practice."**
- The quiz option *"Highlighting the difficult sentence"* and the demo line about *"highlighting"* echo "-ing" phrasing but aren't analytical filler.

#### (g) Repetitive sentence rhythms
The recurring shape is: short declarative → short declarative → contrast/negation. ("The three paragraphs feel like understanding. Nothing was actually read, and nothing sticks.") Across sections this cadence becomes hypnotic and machine-like. Fix by varying sentence length and occasionally letting a longer, warmer sentence breathe.

#### (h) Other Wikipedia "Signs of AI writing" checks
- **Formatting overkill:** the ✓ tick list in Privacy (see Part 4) is the formatting tell most present here.
- **Compulsive summary / restatement:** the Open-source section restates "the server is not open source" twice in consecutive sentences — a duplicated-sentence editing artifact.
- **AI vocabulary** (delve, tapestry, pivotal, underscore): largely absent — good.
- **False ranges** ("from X to Y"): not prominent — good.

### PART 2 — Tone & accessibility

**Is it too technical? Yes.** The site reads like the abstract of a reading-science paper. Examples to fix:
- *"Working memory holds a handful of items at once. When a sentence keeps you waiting for its verb, or a paragraph introduces four new terms in a row, that budget runs out before the meaning does…"* — a lovely idea buried in cognitive-load jargon. Rewrite → **"You know the feeling: three paragraphs in, you realize you've been reading words without taking any of it in. That's the moment alcoia catches."**
- *"Pace against the difficulty of the paragraph, against your own usual rate, scrolling back to re-read, selecting or copying, leaving the tab."* → **"It watches simple things — how fast you're reading, whether you've slowed down, whether you keep scrolling back up."**
- *"randomised trial found that interrupting a drifting reader with just-in-time questions recovered comprehension losses (d = 0.47)"* — the effect size belongs on the /research page, not front-page body copy. On the homepage → **"In one controlled study, nudging a distracted reader with a quick question at the right moment measurably restored how much they understood."** Keep "d = 0.47" for the research page.

**Over-reliance on ChatGPT comparisons: Yes.** Two full sections ("Paste it in, get an answer, retain nothing" and "Why not just use ChatGPT?") define the product by what it isn't. One is persuasive; two is defensive. Merge into a single, confident section and lead with what alcoia *does*.

**Does it explain value in plain terms for a normal student/instructor? Partially.** The philosophy ("AI that makes you need AI less") never actually appears on the homepage in those words — consider adding it as a memorable tagline. The mechanism is over-explained; the *outcome* ("walk into the exam actually remembering the reading") is under-explained.

**Student vs. instructor value — unequal.** The student story gets the whole demo and hero. The instructor story is compressed to one sentence: *"A receipt students can submit as evidence a reading happened, and an aggregate, anonymous view of which paragraphs a class struggled with."* That's a compelling, specific promise — it deserves far more room. Gaps for instructors: How is it assigned? What does the class dashboard look like? How long to set up? Is student data really anonymous? The homepage answers none of these; the /for-educators page must.

### PART 3 — UI/UX best practices for this category (2026)

**Hero section.** Above-the-fold still matters. Nielsen Norman Group's eyetracking research found users spend about 57% of page-viewing time above the fold and 74% within the first two screenfuls — so the first screen carries disproportionate weight, and CTAs above the fold are widely reported to outperform below-fold placement. Practical 2026 guidance: design the first screen for a 1366×768 laptop and a ~390×844 phone; keep the headline (5–12 words), subhead (1–2 sentences), a single primary CTA, and one trust signal all in the first viewport. Use `svh` units for full-height heroes (not `vh`) so browser chrome doesn't push the CTA out of frame. Consolidate the nav to one row (a stacked nav + utility bar can eat 180–250px of height). Beware the "false bottom" — a hero that looks complete but hides content and the CTA below the fold.

**Scroll animations.** Fade-in-on-scroll is now viewed skeptically: it delays content, can hide text from users who land mid-fade, and reads as a template default. Current best practice is to (1) respect `prefers-reduced-motion` and swap movement for a simple opacity change or no motion, (2) keep any motion short and subtle, (3) never gate meaningful text behind an animation, and (4) provide pause/stop for anything looping. "The same fade-in on every element" is explicitly listed as a 2026 AI-slop tell. This is an accessibility issue, not a preference: per Agrawal et al. (Archives of Internal Medicine, NHANES 2001–2004, n=5,086), "35.4% of US adults aged 40 years and older (69 million Americans) had vestibular dysfunction" — large, fast motion can trigger real discomfort for them.

**Design patterns that look human, not templated.** The 2026 "AI slop" fingerprint is well documented: Inter font, indigo/purple gradients, three rounded feature cards in a row, glassmorphism, one giant centered Lucide icon, permanent dark mode, and identical card grids. Ironically, the emerging "tasteful" default (cream background + Instrument Serif + sage-green accent) is now *also* becoming a tell. The way to look human: commit to one deliberate typographic and color choice tied to something true about the product (reading, focus, calm), break the three-card grid with asymmetry, and use real coded UI instead of decorative imagery.

**Imagery on a text-heavy product.** For a product about reading and thinking, generic illustrations and stock photos hurt more than help — audiences can now spot AI/stock imagery, and it undermines authenticity. The strongest visual is the product itself: the live in-page "quick check" demo alcoia already has is exactly right, and the product's own guidance (product/data visuals must be coded UI components, not AI images) is correct and on-trend. Lean into animated-but-real UI, annotated screenshots of the class dashboard, and typographic treatment of real reading passages.

**Docs for two audiences.** Best practice (Atlassian/Mintlify/GitBook patterns): shared foundations, then split by audience where journeys genuinely diverge. For alcoia, a shared "Get started (install + first check)," then separate "For students" and "For instructors" tracks, organized by user goal ("Assign a reading," "See where my class struggled") rather than by feature. Use progressive disclosure — basics first, advanced later.

**Freemium extension conversion.** Realistic free-to-paid for extensions is well below the SaaS norm. Per developer ktg0215 (DEV Community): "Across all five extensions, my overall free-to-paid conversion rate is 0.8%… For SaaS, 2-5% free-to-paid is considered decent. For Chrome extensions, I've come to believe 0.5-2% is realistic." So the free tier must be genuinely useful (alcoia's is) and the paywall should trigger at a natural limit, not mid-task. What converts: a store listing with 3–5 real screenshots and a demo video, minimal/justified permissions in plain language, an active issue tracker, and visible trust signals. What kills trust: vague permissions, over-asking, and unrecognized payment domains. alcoia's live homepage demo is a real conversion asset — feature it even more prominently.

### PART 4 — Specific issues on this site

**Hero spacing / CTA below the fold.** The hero stacks: nav → wordmark (repeated) → pronunciation line ("/ælˈkɔɪ.ə/·al-KOY-uh") → H1 → subhead → two buttons → trust line → then a large live-demo block. The repeated wordmark and the pronunciation guide are pure vertical cost above the headline, and generous top padding compounds it — on a 1366×768 laptop this risks pushing "Add to Chrome (free)" to the fold line or below. Specific fixes:
- Remove the second (in-hero) wordmark; the nav already has it.
- Move the pronunciation guide inline next to the H1, or drop it from the hero (put it on /about).
- Cut hero top padding roughly in half; target the H1 starting within ~120–160px of the viewport top on desktop.
- Use `min-height: 100svh` (not `vh`) if you want a full-height hero, and ensure both buttons sit within the first viewport at 768px height.
- Keep the "Works on any page. No account needed. No camera." trust line — it's excellent and belongs above the fold.

**Checkmark ✓ patterns.** The Privacy section uses a four-item ✓ list. Green-tick lists are now a documented AI/template tell, and this one sits on the most trust-critical section of the site. Recommendation: replace the ✓ glyphs with a more distinctive, brand-specific treatment (e.g., plain statements with a subtle custom mark, or a "what leaves / what stays" two-column layout). The *content* is great — the honesty about passage text leaving the machine "at the same size as everything above it" is exactly right — just don't render it as a checkmark checklist. Re-check /start-pilot, /teams/checkout, and any confirmation screen for ✓ ticks, which read as AI-generated on verification pages specifically.

**Guide/how-to completeness.** Could not verify /how-to-use, /docs, /for-students, /for-educators, /for-institutions, /roadmap, /changelog, or /faq directly. These must be checked for stub content. The footer links to all of them, so empty or thin pages would be conspicuous. Priority checks: /for-educators (the instructor value prop is currently one sentence) and /docs#install (the hero's primary CTA points here — it must be complete and frictionless).

**Legal pages.** Could not fetch /legal/* directly, but the homepage footer already exposes a red flag: the address block reads literally **`alcoia` / `[ TODO: street address ]` / `[ TODO: city, postal code, country ]`**. A missing imprint address is a legal problem in some jurisdictions (e.g., German Impressum rules) and a trust problem everywhere. Given this placeholder leaked to the footer, /legal/imprint and /legal/dpa are the highest-risk pages for unfinished address/legal text and must be reviewed before launch.

**Exposed internal / dev details.**
- `[ TODO: … ]` placeholders in the footer (above).
- Open-source section has an **unclosed parenthesis**: *"(including the one file that touches the webcam, linked above."* and a **duplicated sentence** ("The server… is not open source" twice) — an editing artifact.
- The site is served from a **`.workers.dev` preview subdomain** while canonical/OG tags point to `alcoia.com` — a deployment/SEO mismatch that shouldn't be the public URL.
- A **"CJ" portfolio logo** in the footer links to `cjayy77.github.io/cj-portfolio/` — fine as a credit, but decide whether a personal-portfolio backlink belongs on a product site's footer at launch.

**Is it boring? Somewhat — but fixably.** The site is serious and trustworthy, which suits a comprehension tool; the risk is that wall-to-wall gray text in an even rhythm reads as flat. It is NOT boring where the live demo lets you experience the "quick check" — that interactive moment is the single best thing on the page. To add life without gimmicks: (1) lead with the interactive demo even higher; (2) inject one or two genuinely human, first-person founder lines (the Research disclaimer already proves this voice works); (3) show the real instructor dashboard as a coded, annotated component; (4) vary the visual rhythm so every section isn't header-over-two-columns; (5) give the philosophy line ("AI that makes you need AI less") a prominent, confident placement. Avoid: bouncy scroll animations, gradient orbs, stock imagery, and emoji headers — all of which would undermine the earned seriousness.

---

## Recommendations (prioritized)

**Stage 1 — Ship-blockers (do before any launch):**
1. Replace the `[ TODO: street address ]` / `[ TODO: city, postal code, country ]` footer placeholders with a real address; review /legal/imprint and /legal/dpa for the same.
2. Fix the Open-source section's unclosed parenthesis and remove the duplicated "server is not open source" sentence.
3. Move the site off the `.workers.dev` preview URL to `alcoia.com` (matching the canonical) before promoting it.
4. Verify /docs#install (the hero's main CTA target) is complete and frictionless.

**Stage 2 — De-AI the copy (highest engagement ROI):**
5. Cut the contrast constructions from nine to at most two on the homepage; apply the same rule to every subpage.
6. Rename "Three honestly labelled stages" and "Privacy, explained."
7. Break the rule-of-three cadence and vary sentence length.
8. Move cognitive-science vocabulary (working-memory framing, "d = 0.47") to /research; lead with plain-language outcomes.
9. Merge the two anti-ChatGPT sections into one and lead with what alcoia does.

**Stage 3 — Hero & layout:**
10. Remove the in-hero wordmark and pronunciation line; halve top padding; confirm both CTAs sit above the fold at 1366×768 using `svh`.
11. Replace the ✓ checkmark privacy list with a distinctive non-template treatment; audit confirmation screens for ✓ ticks.

**Stage 4 — Audience clarity & engagement:**
12. Expand the instructor story into a real section: assigning a reading, the anonymous class dashboard (as a coded UI component), setup time, and a data-privacy reassurance.
13. Give students a clear outcome-first pitch ("walk into the exam actually remembering the reading").
14. Feature the interactive demo even more prominently; add one or two first-person founder lines; surface the "AI that makes you need AI less" philosophy.
15. Ensure all animation respects `prefers-reduced-motion`; avoid fade-in-on-scroll for meaningful text.

**Benchmarks that would change these recommendations:**
- If analytics show the hero CTA already gets strong above-fold clicks on laptop, deprioritize Stage 3.10.
- If free-tier activation is healthy (users completing a first "quick check") but paid conversion lags the ~0.5–2% extension band, focus on the natural-limit paywall trigger, not the marketing copy.
- Once alcoia has its own participant data, replace the "not ours yet" framing with real numbers — that single change will do more for credibility than any copy edit.

---

## Caveats
- **Only the homepage was directly audited.** The subpages (/how-it-works, /for-educators, /for-students, /pricing, /for-institutions, /privacy, /how-to-use, /docs, /faq, and all /legal/* pages) could not be fetched — the site is an unindexed Cloudflare Worker, and both the fetch tool and a dedicated subagent were blocked. All Part-1 pattern findings are from verified homepage copy; subpage findings are inferences that must be confirmed by re-running the same checklist directly against each page. The instructor/legal/docs completeness checks in Part 4 are therefore flagged as "verify," not "confirmed."
- Some UX statistics come from vendor/agency blogs rather than primary research; where a specific number is cited, prefer the primary sources named above (Nielsen Norman Group for attention distribution; Agrawal et al. / Archives of Internal Medicine for vestibular prevalence; ktg0215/DEV for extension conversion). The "d = 0.47" just-in-time-question trial and any "interactive micro-demo lift" figure could not be independently verified against a primary source and should be confirmed before quoting externally.
- Conversion benchmarks for extensions vary widely by category; the 0.5–2% band is a realistic planning range, not a guarantee.
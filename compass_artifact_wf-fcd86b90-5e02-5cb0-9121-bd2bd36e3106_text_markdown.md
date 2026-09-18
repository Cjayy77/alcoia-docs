# Legal & Compliance Requirements for an AI-Powered EdTech Browser Extension + SaaS: Reality Check on the "10 Ways Your Vibecoded App Gets Sued"

## TL;DR
- **Most of the viral video's 10 claims map to real legal obligations, but the framing and penalty figures are frequently wrong or oversimplified.** The single most consequential correction: the FTC "click-to-cancel" rule the video likely references was **vacated in full by the Eighth Circuit on July 8, 2025** and is not currently enforceable — but California's Automatic Renewal Law (stricter than ever since July 1, 2025), ROSCA, and roughly 30 states' auto-renewal statutes still impose nearly identical duties.
- **The genuine launch-blockers for this specific product are the child/student-data rules, not the consumer-marketing items:** COPPA (under-13), FERPA + SOPIPA/state student-privacy laws (K-12/university data), GDPR (EU students, with a full DPA and lawful-basis analysis), and a compliant Chrome Web Store data-disclosure + privacy policy. AI-specific disclosure is real but modest (EU AI Act Art. 50 from Aug 2, 2026; California/Colorado bot laws); "AI with no self-harm response" is now a real statutory duty in California (SB 243) and New York for "companion" chatbots.
- **A merchant of record (Creem.io) offloads tax/VAT, PCI and chargebacks — but does NOT shield you from auto-renewal/negative-option liability** (in *FTC v. Paddle*, the FTC pursued the merchant of record as a primary actor) or from any of the privacy, student-data, AI, or accessibility obligations, which all remain squarely on you.

## Key Findings

**The 10 viral claims, adjudicated:**
1. **No Privacy Policy** — TRUE requirement. Triggered by collecting personal data. Legal bases: California's CalOPPA (Bus. & Prof. Code §22575), CCPA/CPRA, GDPR Arts. 12–14, plus Chrome Web Store policy. Genuine launch blocker.
2. **No "we collect user data" disclosure** — Same legal roots; this is a subset of #1. Required.
3. **No mention of using AI** — PARTIALLY TRUE and emerging. No blanket "disclose AI in your privacy policy" law today, but multiple specific mandates apply (EU AI Act Art. 50, California SB 243, Colorado, CCPA ADMT rules). Medium priority now, rising.
4. **No mention of third-party data collectors** — TRUE. GDPR Art. 13/28, CCPA, CalOPPA, and COPPA all require disclosing recipients/sub-processors. Required.
5. **Not deleting user uploads** — TRUE. GDPR right to erasure (Art. 17), CCPA deletion right, SOPIPA/FERPA deletion-on-request, COPPA retention limits. Required.
6. **Storage bucket = public** — TRUE and serious. Security + breach-notification duties (GDPR Arts. 32–34, US state breach laws, COPPA/CCPA security). High priority.
7. **Fake testimonials** — TRUE. FTC rule 16 CFR Part 465, effective Oct 21, 2024; civil penalty up to $53,088 per violation (2025 figure). Real and enforced.
8. **Cancelling harder than signup** — MISLEADING as stated. FTC click-to-cancel rule VACATED July 2025; but CA ARL, ROSCA, and state laws still require symmetrical cancellation. Required via state law.
9. **Auto-renew without reminder** — TRUE under state law (esp. CA ARL as amended by AB 2863, eff. July 1, 2025), not under vacated federal rule.
10. **AI with no self-harm response** — NOW TRUE in some states for companion chatbots (CA SB 243, NY GBL §1700). Whether it applies depends on product design.

## Details

### 1–2. Privacy policy and data-collection disclosure — HARD REQUIREMENT, LAUNCH BLOCKER
There is no single "you must have a privacy policy" federal US law, but several overlapping laws make one mandatory for this product:
- **CalOPPA (Cal. Bus. & Prof. Code §22575):** Requires any operator of a commercial website/online service that collects personally identifiable information about California residents to conspicuously post a privacy policy. Verbatim, §22575(a): an operator "that collects personally identifiable information through the Internet about individual consumers residing in California who use or visit its commercial Web site or online service shall conspicuously post its privacy policy." It must identify categories of PII collected and categories of third parties it's shared with (§22575(b)(1)), describe the review/change process, describe how material changes are communicated, state an effective date, and disclose how it responds to Do Not Track signals (§22575(b)(5)). Enforced via California's Unfair Competition Law (Bus. & Prof. Code §17200), with penalties up to $2,500 per violation under §17206(a). There is a 30-day cure period after notice of noncompliance (§22575(a)).
- **GDPR Articles 12–14:** Require a transparent privacy notice given at the point of collection with a closed, exhaustive list of ~14 mandatory items: controller identity, DPO contact, purposes and lawful basis, recipients/categories of recipients, international transfers, retention period, data-subject rights, right to complain to a supervisory authority, and existence of automated decision-making. Missing items make the notice non-compliant; DPAs (CNIL, German authorities) routinely sanction for opacity and missing items.
- **CCPA/CPRA:** Requires a privacy policy with enumerated disclosures; as of the 2025 CPPA regulations (effective Jan 1, 2026), links to the privacy policy must appear on every page where personal information is collected, not just the homepage.
- **Chrome Web Store:** Independently requires a privacy policy linked in the Developer Dashboard for any extension handling user data.

**Penalty context:** CalOPPA/UCL up to $2,500 per violation. CCPA/CPRA statutory penalties are $2,500 per violation and $7,500 per intentional violation or any violation involving minors' (under-16) data (Cal. Civ. Code §1798.155) — note the CPPA inflation-adjusts these (approximately $2,663/$7,988 for 2025). The CPRA deleted the CCPA's mandatory 30-day cure period (effective Jan 1, 2023); a cure opportunity is now discretionary. GDPR fines reach the tiers described in §14 below.

### 3. Disclosing AI/LLM use — EMERGING, MEDIUM PRIORITY
There is **no general law requiring you to say "we use AI" in your privacy policy** today, but a cluster of specific mandates converge:
- **EU AI Act Article 50(1):** Providers of AI systems that interact directly with people must design them so users are informed they are interacting with AI, unless it's obvious to a reasonably well-informed person. This obligation applies from **August 2, 2026**. Article 50(2) also covers marking of AI-generated content. It applies to non-EU providers whose output is used in the EU. This is a product/UI disclosure, not strictly a privacy-policy line.
- **California SB 243 (companion chatbots, eff. Jan 1, 2026)**, **Colorado SB 24-205 / its 2026 replacement SB 26-189 (eff. Jan 1, 2027)**, and **Utah** require disclosure that a user is interacting with AI in certain contexts.
- **CCPA ADMT regulations (finalized Sept 2025, phasing in from Jan 1, 2026):** Require disclosures and opt-out/access rights when automated decision-making technology is used for "significant decisions" about consumers. A comprehension-scoring tool could implicate these if its outputs drive significant decisions about students.
- **GDPR Art. 13(2)(f)/22:** Requires disclosure of automated decision-making with legal or similarly significant effects, and meaningful information about the logic.

For a text-comprehension tool that plainly labels itself as AI-powered, the practical compliance step is a clear in-product statement plus a privacy-policy section describing that submitted text is processed by an LLM (Groq) and naming that sub-processor.

### 4. Third-party / sub-processor disclosure — HARD REQUIREMENT
- **GDPR Art. 28:** You (as processor for schools) may not engage a sub-processor (Groq, Creem.io, hosting) without the controller's prior specific or general written authorization, and you remain fully liable for the sub-processor's compliance. Your DPA must contain the eight mandatory Art. 28(3) terms (process only on documented instructions; confidentiality; Art. 32 security; sub-processing controls; assist with data-subject rights; assist with breach/DPIA; delete or return data at end; submit to audits). Cross-border transfers to a US-based Groq require an Art. 46 mechanism (2021 SCCs Module 2 or 3 + a transfer impact assessment) unless covered by an adequacy decision/Data Privacy Framework certification.
- **CalOPPA & CCPA:** Require disclosing categories of third parties; CCPA requires service-provider/contractor contracts with specified terms.
- **COPPA (2025 amendments):** Direct notice to parents/schools must now identify the third parties (identities or specific, "meaningful and specific" categories) to whom a child's data is disclosed and the purpose.

### 5. Deleting user uploads / retention & erasure — HARD REQUIREMENT
- **GDPR Art. 17 (right to erasure)** and **Art. 5(1)(e) (storage limitation):** Data must not be kept longer than necessary and must be deleted on valid request.
- **CCPA/CPRA:** Consumer right to deletion.
- **SOPIPA:** Operator must delete a student's covered information at the request of the school/district.
- **COPPA:** Retain children's data only as long as necessary and delete it; parents can require deletion; the 2025 amendments require a written retention policy and prohibit indefinite retention.
- **FERPA context:** Your DPA with schools must specify return/deletion of education records.

Instructor-uploaded course documents and student comprehension data are exactly the categories these rules target. Build deletion tooling before launch.

### 6. Public storage bucket / security & breach notification — HIGH PRIORITY
- **GDPR Art. 32:** "Appropriate technical and organisational measures." **Art. 33:** Notify the supervisory authority within 72 hours of becoming aware of a breach likely to risk individuals; **Art. 34:** notify affected individuals if high risk. Failure to notify is itself a violation (up to €10M/2% tier). Pseudonymized data with accessible keys still counts as personal data and a leak still triggers notification.
- **US state breach-notification laws** (all 50 states) require notice to affected residents, typically within 30–60 days.
- **COPPA/CCPA/SOPIPA** each impose reasonable-security duties. CCPA also creates a **private right of action for breaches** ($100–$750 per consumer per incident) — the one CCPA provision individuals can sue on directly.
- **Chrome Web Store** requires data transmitted over HTTPS/WSS and stored encrypted at rest (strong methods such as RSA/AES).

A public bucket exposing student data is close to a worst-case fact pattern here — it combines a security-measures failure, a breach-notification trigger, and (for minors) heightened penalties.

### 7. Fake testimonials — HARD REQUIREMENT, REAL PENALTIES
The **FTC Trade Regulation Rule on the Use of Consumer Reviews and Testimonials, 16 CFR Part 465**, took effect **October 21, 2024**. It bans fake/AI-generated reviews, buying positive or negative reviews conditioned on sentiment, undisclosed insider reviews, company-controlled "independent" review sites, review suppression through threats/intimidation, and buying fake social-media indicators. Civil penalties for knowing violations are inflation-adjusted; in its **December 22, 2025 warning letters to 10 companies**, the FTC (in a letter signed by Janice Kopec, acting associate director of the Division of Advertising Practices) warned verbatim that "Continued violations may result in legal action, including civil penalties of up to **$53,088 per violation**" (the 2024 figure was $51,744). Penalties are counted per violation, so fabricated reviews at scale produce ruinous numbers. Do not seed the launch with fake reviews or incentivized-for-sentiment testimonials.

### 8. Cancellation as easy as signup — STATE LAW, NOT CURRENT FEDERAL RULE
The FTC's revised Negative Option Rule ("click-to-cancel," 16 CFR Part 425) was **vacated in its entirety by the U.S. Court of Appeals for the Eighth Circuit on July 8, 2025** (*Custom Communications, Inc. v. FTC*, No. 24-3137), on procedural grounds (the FTC skipped a required preliminary regulatory analysis for a rule with >$100M compliance cost), just days before its July 14, 2025 enforcement date. **So the federal "click-to-cancel" rule is not enforceable.** BUT the underlying obligation survives through other law:
- **ROSCA** (Restore Online Shoppers' Confidence Act) still requires cancellation to be "at least as easy to use as the method the consumer used to initiate," and the FTC continues to enforce case-by-case. In April 2025 the FTC sued Uber under the FTC Act and ROSCA over its subscription billing/cancellation practices, and in August 2025 the California Automatic Renewal Task Force announced a $7.5M ARL settlement with HelloFresh (LA/Santa Clara county DAs).
- **California ARL (Bus. & Prof. Code §17600 et seq.), as amended by AB 2863 (eff. July 1, 2025):** requires cancellation through the same channel used to sign up ("click to quit"), affirmative consent, and more.
- **State expansion in 2025:** new or updated auto-renewal laws took effect or advanced in Arkansas, California, Colorado, Connecticut, Maryland, Massachusetts, Minnesota and Utah. New York's amended ARL took effect Nov. 5, 2025 requiring in-app "cancel" buttons; Colorado requires one-step online cancellation.

So the obligation is real via state law; only the specific federal rule died.

### 9. Auto-renew reminders — STATE LAW REQUIREMENT
- **California ARL / AB 2863 (eff. July 1, 2025):** now covers free-to-pay conversions (free trials), requires express affirmative consent, retention of consent proof (3 years or 1 year post-termination, whichever is longer), clear-and-conspicuous notice of material pricing/service changes, and **annual reminders** stating renewal frequency, amount, and cancellation instructions. ARL violations feed into UCL/consumer-protection penalties and have generated a steady stream of class actions.
- Various other states (see §8) require renewal reminders, often for longer-term or auto-renewing contracts.

Note: a merchant of record like Creem.io may handle the billing mechanics but, per *FTC v. Paddle* (2025), does not absorb your legal responsibility for these disclosures.

### 10. AI with no self-harm response — NOW A REAL STATUTORY DUTY IN SOME STATES
This is no longer purely theoretical:
- **California SB 243 (signed Oct 13, 2025; eff. Jan 1, 2026):** "Companion chatbot" operators must maintain a protocol to prevent production of suicidal-ideation/self-harm content and to refer at-risk users to crisis services, publish the protocol on their website, disclose AI status, protect known minors (reminders to take breaks, no sexual content), and (from July 1, 2027) file annual reports with the California Office of Suicide Prevention. It includes a private right of action for families.
- **New York AI Companion law (Gen. Business Law §1700 et seq., eff. Nov 5, 2025):** requires reasonable efforts to detect and address self-harm plus AI-status disclosure.
- **Colorado HB 26-1263 "Chatbot Safety Act" (signed 2026):** requires age estimation, AI disclosure, minor safeguards, and suicide/self-harm response protocols.

**Key nuance:** these laws target "companion" chatbots designed for human-like social/emotional interaction. SB 243 expressly excludes customer-service bots, single-purpose/operational bots, and video-game bots. A text-comprehension tutor is likely **outside** the strict "companion chatbot" definition. But because this product is used by minors and processes free-text that could contain disclosures of self-harm, implementing a crisis-referral safety response is strongly advisable as risk mitigation (negligence exposure) and to stay ahead of expanding legislation, even if you fall outside the literal definitions today.

### 11. COPPA — LAUNCH BLOCKER IF UNDER-13 USERS
- **Trigger (16 CFR §312.3):** COPPA applies if your service is "directed to children" under 13, OR you have "actual knowledge" you are collecting personal information from a child under 13. A general-audience/education tool sold into K-12 that will foreseeably reach under-13 students is squarely in the danger zone.
- **"Actual knowledge":** you don't have to investigate ages on a general-audience site, but if a school tells you the users are under-13 elementary students, or you collect birthdates showing it, that is actual knowledge.
- **School consent pathway:** For ed-tech used in schools, the school can provide consent in lieu of parents, **but only** if the data is used solely for the educational purpose authorized by the school and not for commercial purposes — you must give the school the COPPA-required notice of your data practices. If you also run a direct-to-consumer path, you cannot rely on school consent for that path and need verifiable parental consent. The FTC has confirmed these two pathways cannot share a single consent architecture.
- **2025 amendments (published Apr 22, 2025; effective June 23, 2025; compliance deadline April 22, 2026):** expanded "personal information" to include biometric and government identifiers; require separate opt-in consent for third-party disclosures (e.g., targeted advertising); require a written data-retention policy and prohibit indefinite retention; strengthen security and third-party vetting; and require direct notice to identify third parties. Civil penalties up to $53,088 per violation.

### 12. FERPA — DPA + "school official" status required for K-12/university data
FERPA binds schools, but flows to you contractually. To lawfully receive student "education records" without individual consent, you must qualify for the **school official exception (34 CFR §99.31(a)(1))**, meaning you: (1) perform an institutional service/function the school would otherwise use employees for; (2) are under the **direct control** of the school as to use and maintenance of education records; (3) meet the criteria in the school's annual FERPA notice for "school official with legitimate educational interest"; and (4) use education records only for authorized purposes and do **not** re-disclose them.

A compliant DPA must: designate you as a school official with legitimate educational interest; limit use to the contracted purpose; prohibit re-disclosure and unauthorized secondary use (e.g., training your models on student data, or advertising); require security safeguards; require return/deletion of records; and restrict sub-processors. Using student data for non-educational purposes (like product development) can forfeit the exception. FERPA's ultimate penalty is loss of federal funding to the school (rarely invoked), but a FERPA breach kills school contracts and triggers state-law and contractual liability — so it functions as a hard commercial gate.

### 13. SOPIPA and state student-privacy laws
- **California SOPIPA (Cal. Bus. & Prof. Code §22584, eff. Jan 1, 2016):** Applies to operators of sites/services/apps with **actual knowledge** the product is used primarily for, and was designed/marketed for, K-12 school purposes. Prohibits targeted advertising to students, building non-educational profiles, and selling student data; requires reasonable security; and requires deletion of student data at the school's request. Critically, SOPIPA binds the **vendor directly** (unlike FERPA, which binds schools), so you are personally on the hook.
- **State proliferation:** More than 40 states have enacted student-privacy laws, many modeled on SOPIPA, plus New York Education Law §2-d and Illinois SOPPA (which requires public breach notification and published lists of subcontractors). If you sell nationally into K-12 you must comply with the strictest common denominator.

### 14. GDPR for EU students
- **Roles:** The school is typically the controller; you are the processor. Requires a full Art. 28 DPA (see §4).
- **Lawful basis:** For public schools, processing is usually "public task" (Art. 6(1)(e)) or "legitimate interests" (Art. 6(1)(f)); consent is generally a poor basis in the education context because of the power imbalance and because subjects are children. For children's data, Art. 8 sets the digital-consent age between 13 and 16 depending on the member state.
- **Children's data** merits specific protection under GDPR; privacy notices addressed to children must use clear, age-appropriate language.
- **Pseudonymized aggregate data:** Pseudonymized data **still counts as personal data** under GDPR (Recital 26) if re-identification is possible with the key; only truly anonymized (irreversible) data falls outside GDPR. So aggregate comprehension analytics keyed to student IDs remain regulated.
- **Fines (Art. 83):** two tiers, "whichever is higher." Lower tier (Art. 83(4)) — up to **€10M or 2% of global annual turnover** — covers security, breach-notification, DPIA, records, and processor obligations (Arts. 8, 11, 25–39, 42, 43). Upper tier (Art. 83(5)) — up to **€20M or 4% of global annual turnover** — covers breaches of the basic principles and lawful basis/consent (Arts. 5, 6, 7, 9), data-subject rights (Arts. 12–22), and international-transfer rules (Arts. 44–49). Non-compliance with a supervisory-authority order is also upper-tier (Art. 83(6)).

### 15. Chrome Web Store extension disclosure
- **Privacy policy** must be posted and linked in the Developer Dashboard, and must comprehensively disclose how the extension collects, uses, and shares user data and all parties data is shared with.
- **Data-handling certification** in the dashboard's Privacy tab must be completed and must match the privacy policy; reviewers in 2026 cross-reference certifications against actual extension behavior.
- **Prominent disclosure + consent:** For data collection, a prominent in-UI disclosure and affirmative consent are required *before* collection — and this disclosure "must not be located only in a privacy policy, terms of service, or similar document."
- **Limited Use policy:** Data may only be collected/used for the extension's disclosed single purpose and related operational purposes; selling data, using it for creditworthiness, or transferring to data brokers is prohibited. A Limited Use affirmative statement must appear on your site/privacy policy.
- **2026 policy update:** ALL data collection must now be prominently disclosed regardless of whether it relates to the single purpose; and there is a new prohibition on extensions designed to circumvent AI-service safety guardrails.

Non-compliance means removal/rejection from the store — an existential distribution risk for a browser extension, and effectively a launch blocker.

### 16. Enforceable Terms of Service
- **Use clickwrap, not browsewrap.** Courts (e.g., *Meyer v. Uber*, 2d Cir. 2017; *Feldman v. Google*; *Specht v. Netscape*) enforce agreements where the user had **reasonable notice** of the terms and **manifested assent** via an affirmative act (checking a box / clicking "I agree" adjacent to a conspicuous hyperlink). Browsewrap (passive footer links) is often unenforceable.
- **Best practices for enforceability:** conspicuous, plain-language presentation; hyperlink to terms immediately adjacent to the assent button; no pre-checked boxes; require re-assent when terms materially change; and keep records of who accepted which version and when (screenshots of the flow + timestamps). Courts have held that without proof of who accepted which version, the agreement is not enforceable.
- **Unconscionability:** avoid hidden, oppressive, or one-sided terms; overly aggressive arbitration/class-waiver or liability clauses can be struck as unconscionable, especially against consumers and in a product used by minors. Terms cannot override non-waivable statutory rights (GDPR/CCPA/COPPA/FERPA), and pre-checked consent boxes are void under GDPR/CCPA.

### 17. Accessibility (ADA / WCAG / European Accessibility Act)
- **US ADA Title III:** Courts increasingly treat commercial websites/apps as places of public accommodation and use **WCAG 2.1 AA** as the de facto standard. Litigation is at record levels: plaintiffs filed **3,117 website-accessibility lawsuits in federal court in 2025, a 27% increase over the 2,452 filed in 2024** (Seyfarth Shaw), and counting state-court filings the total exceeded ~5,000; average settlements run around $30,000 plus attorneys' fees. Selling to public universities/schools also implicates ADA Title II and Section 504 — the DOJ's April 2024 Title II rule mandates WCAG 2.1 AA for state/local government entities (compliance deadlines April 24, 2026 for larger entities, 2027 for smaller, with a DOJ interim rule extending some deadlines). Schools frequently require accessibility conformance (a VPAT/ACR) as a procurement condition.
- **European Accessibility Act (Directive 2019/882):** Enforceable since **June 28, 2025**. Requires covered digital products/services sold to EU consumers to meet accessibility requirements, with **EN 301 549 (incorporating WCAG 2.1 AA)** as the presumed-conformance standard. Applies to non-EU companies selling into the EU. Microenterprises providing services (<10 employees and <€2M turnover) may be exempt, subject to per-member-state transposition. The first EAA-related lawsuits were filed on **November 12, 2025**, when disability-rights organizations ApiDV and Droit Pluriel filed emergency injunctions in French Commercial Court against retailers Auchan, Carrefour, E. Leclerc and Picard; the Carrefour case was the first decided, with the judge ruling an e-commerce site "must be totally accessible."

For an education product, accessibility is both a legal requirement and a near-universal procurement gate — treat WCAG 2.1 AA (ideally 2.2 AA) as a launch requirement.

### Cross-cutting: the merchant of record (Creem.io)
Using an MoR **shifts to the MoR**: sales tax/VAT/GST collection and remittance, PCI-DSS compliance, payment-fraud liability, and chargeback/refund handling (Lemon Squeezy and Paddle both state they assume these because they are technically the seller). It does **NOT shift**: your privacy, student-data, AI-disclosure, accessibility, or consumer-protection obligations. Critically, in **FTC v. Paddle** — announced **June 17, 2025** with a **$5 million settlement** — the FTC pursued the merchant of record "as a primary actor rather than a third-party facilitator" under ROSCA and permanently banned Paddle from processing payments for tech-support telemarketers (Restoro/Reimage). This is the opposite of a liability shield: both the MoR and the underlying seller can be exposed, and the underlying seller always remains responsible for the legality and marketing of its own product. Review the specific Creem.io contract for carve-outs; MoR agreements commonly leave the seller responsible for product content, warranties, and lawful disclosures.

## Recommendations

**Stage 0 — Do not launch without these (hard blockers):**
1. Publish a compliant, layered privacy policy satisfying CalOPPA + GDPR Arts. 13/14 + CCPA, naming sub-processors (Groq, Creem.io, hosting) and describing LLM processing of submitted text.
2. Stand up a **Data Processing Agreement** for schools that makes you a FERPA "school official," satisfies GDPR Art. 28(3), and includes SOPIPA commitments (no ad targeting, no selling data, no non-educational profiling, deletion on request, no training models on student data without authorization).
3. Implement **data deletion** (erasure on request, retention limits, delete-on-school-request) and **security** (private buckets, encryption in transit/at rest, access controls) before touching student data. Establish a breach-response plan meeting the 72-hour GDPR clock and state timelines.
4. Decide your **under-13 posture.** Either (a) contractually restrict to 13+ and enforce it, or (b) build the COPPA school-consent flow with COPPA-required notice to schools and no commercial data use. This is the single biggest legal decision for the product.
5. Ship a **Chrome Web Store**-compliant listing: privacy policy link, accurate data-handling certification, in-UI prominent disclosure + consent (not buried in the policy), and a Limited Use statement.
6. Implement **clickwrap** ToS with logged assent and versioning; avoid unconscionable terms and pre-checked boxes.
7. Meet **WCAG 2.1 AA** (target 2.2 AA) and prepare a VPAT/accessibility statement for EAA and ADA/procurement.

**Stage 1 — Complete within the first compliance cycle:**
8. Build **California ARL-compliant** subscription flows: affirmative consent, same-channel cancellation, annual reminders, change notices, consent recordkeeping — regardless of the MoR. Don't rely on the vacated federal click-to-cancel rule; map against CA, NY, and Colorado specifically.
9. Add a clear **AI disclosure** in-product and a **crisis/self-harm referral** response for free-text inputs (prudent given minor users; positions you for SB 243-style laws and EU AI Act Art. 50 by Aug 2026).
10. Ensure **no fake/incentivized testimonials**; audit any reviews program against 16 CFR Part 465.

**Stage 2 — Monitor and scale:**
11. Track CCPA ADMT rules (phasing from Jan 1, 2026), Colorado SB 26-189 (Jan 1, 2027), EU AI Act Art. 50 (Aug 2, 2026), and the expanding state student-privacy and chatbot-safety laws.

**Thresholds that change the plan:** If you confirm under-13 users → COPPA becomes a hard blocker requiring the full school-consent build. If you sell to EU schools → GDPR DPA + SCCs/transfer analysis for Groq becomes mandatory. If the product adds emotionally interactive/"companion" features → SB 243/NY companion-chatbot duties attach directly. If revenue/headcount exceed the EAA microenterprise thresholds → EAA compliance is non-negotiable.

## Caveats
- **Not legal advice.** This synthesizes statutes, regulator guidance, and court decisions as of September 2026; retain counsel in your operating jurisdiction and in California/EU before launch.
- **The AI-disclosure and chatbot-safety area is moving fast and is partly forward-looking** — several 2026–2027 effective dates and pending rulemakings (CCPA ADMT, Colorado, EU AI Act guidelines) may shift. Federal preemption of state AI laws has been proposed but not enacted.
- **Penalty figures are maximums and inflation-adjusted annually.** Actual exposure depends on intent, number of violations/affected users, and regulator discretion; per-violation math can be extreme but settlements are often far lower.
- **"Unspecified jurisdiction" does not reduce exposure:** GDPR, the EAA, CCPA, COPPA, and FERPA-driven contract terms all apply based on where your users/customers are, not where you are incorporated.
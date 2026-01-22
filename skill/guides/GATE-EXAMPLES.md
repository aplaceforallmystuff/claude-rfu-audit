# Gate Examples: Pass/Fail Cases

This file contains real-world examples of projects passing and failing each gate, to help auditors calibrate their scoring.

---

## Gate 1: The 5 Whys of Utility

### FAIL Example 1: API Wrapper Stopping at Technical Reason

**Project type:** Node.js library wrapping a REST API

**Scenario:** A package that provides a typed TypeScript interface for a third-party weather API.

**Attempted 5 Whys chain:**
1. Why does this exist? → "To make weather API calls easier"
2. Why do people need easier API calls? → "Because the raw API returns untyped JSON"
3. Why does that matter? → "TypeScript developers want type safety"
4. Why is type safety important? → "It prevents runtime errors"
5. Why do we need to prevent runtime errors? → "Because errors are bad"

**Why this FAILS:**
1. Chain stops at technical implementation ("type safety", "prevent errors") without reaching human impact
2. Never articulates what users DO with weather data or why it matters to their life/work
3. Missing the jump from "fewer errors" to "time saved" or "stress reduced" or "more reliable apps"
4. "Errors are bad" is circular reasoning, not bedrock

**Contrast with PASS:** A passing version would continue: "Why do we need reliable apps?" → "So delivery drivers can trust route planning" → "So they get home to family on time" → **BEDROCK: Time/Safety**

---

### FAIL Example 2: Learning Project Without Utility

**Project type:** React dashboard for personal use

**Scenario:** A dashboard showing GitHub stats, built while learning React hooks and state management.

**Attempted 5 Whys chain:**
1. Why does this exist? → "To learn React hooks"
2. Why do you need to learn React hooks? → "To become a better developer"
3. Why do you need to become a better developer? → "To advance my career"
4. Why does career advancement matter? → "So I can earn more money"
5. Final answer: **Money** (bedrock need)

**Why this FAILS:**
1. Chain reaches bedrock (money) BUT only for the creator, not for users
2. Gate 1 tests utility for OTHERS, not personal learning value
3. If no one else would use this (it's personal stats only), it fails regardless of clean 5 Whys
4. This is the "learning disqualifier" - valid motivation but not sufficient for utility gate

**Contrast with PASS:** If the dashboard helped OTHER developers track team metrics to justify hiring requests, then: "Why do they need hiring?" → "To reduce overtime" → **BEDROCK: Time/Autonomy** (for users, not creator)

---

### PASS Example for Contrast: Time-Saving Automation

**Project type:** CLI tool for batch image processing

**5 Whys chain:**
1. Why does this exist? → "To resize and compress images in bulk"
2. Why do people need bulk processing? → "They have 100s of product photos to prepare for web"
3. Why can't they do this manually? → "Would take hours to process each image"
4. Why does saving hours matter? → "They can spend that time on marketing instead"
5. Bedrock: **Time** (getting hours back for higher-value work)

✅ Passes: Clear path from feature → workflow → time savings → human value

---

## Gate 2: The Inversion Test (Via Negativa)

### FAIL Example 1: Trivial Switching Cost to Alternative

**Project type:** CLI tool for formatting JSON

**Scenario:** A command `prettyjson myfile.json` that formats JSON with colors and indentation.

**Inversion analysis:**
- **Would anyone notice if gone?** Mild annoyance
- **What would they use instead?** `jq . myfile.json` (already installed on most dev machines)
- **Switching cost:** Typing 3 extra characters per use
- **Time to switch:** <30 seconds (just use jq instead)

**Why this FAILS:**
1. Alternative (jq) exists and is already installed
2. Switching cost is <5 minutes (immediate, zero migration)
3. Only saves typing 3 characters - pure convenience
4. Quantitative test: Even with 10 uses/day, saves maybe 5 seconds/day = 30 seconds/week = not meaningful

**Contrast with PASS:** If this tool had a SAVED CONFIGURATION for company-specific formatting rules (indentation style, key ordering, color scheme), then switching cost includes recreating that config (>5 min) → would pass.

---

### FAIL Example 2: Minor Inconvenience Only

**Project type:** Browser extension for tab management

**Scenario:** Extension that groups tabs by domain and shows a count badge.

**Inversion analysis:**
- **Would anyone notice if gone?** "I'd miss the badge, but..."
- **What would they use instead?** Native browser tab search (Cmd+Shift+A) or just... looking at tabs
- **Switching cost:** None - it's a nice-to-have visual indicator
- **Impact:** Saves maybe 5 minutes/month finding tabs

**Why this FAILS:**
1. Solves minor inconvenience, not a painful problem
2. Built-in browser features provide 90% of the value
3. Time savings too small to matter (<5 min/month)
4. Quantitative test: 5 min/month saved = 1 hour/year = not worth installing an extension

**Contrast with PASS:** If tabs auto-closed after 30 days and saved state to restore later (preventing browser slowdown and memory crashes), then prevents PAIN (crashes, lost work) → would pass.

---

### PASS Example for Contrast: High Switching Cost

**Project type:** Local Markdown note-taking app with custom organization

**Inversion analysis:**
- **Would anyone notice if gone?** "I'd lose my entire reference system"
- **What would they use instead?** Notion, Obsidian, or plain folders
- **Switching cost:** 4-8 hours to migrate 1000+ notes + rebuild folder structure + recreate custom templates
- **Impact:** Loss of accumulated organizational structure

✅ Passes: Switching cost >4 hours, significant data migration required

---

## Gate 3: The 10-Minute Test

### FAIL Example 1: Long Setup Before First Value

**Project type:** Local AI chat interface

**Scenario:** README says "Easy setup!" but requires Docker, Redis, and API key.

**Timing breakdown:**
1. **Discovery:** 0 min (starting from README)
2. **Comprehension:** 2 min (README is clear)
3. **Installation:**
   - Install Docker: 5 min (if not present)
   - Install Redis: 3 min
   - Get API key: Sign up → email verification → wait for approval → 15-30 min
   - Configure .env file: 2 min
   - Run docker-compose: 3 min
   - **Total: 28-43 minutes**
4. **First Use:** 1 min (sends message, gets response)
5. **Aha Moment:** Unclear - just shows it works, no unique value demonstrated

**Why this FAILS:**
1. Total time >15 minutes (fails threshold)
2. Pre-requisites (Docker, Redis) are project-specific, not domain-standard → must count their installation
3. API key requires waiting for human approval (not instant) → must count actual wait time
4. Even if timing was OK, no clear aha moment - just "chat works" (not differentiated from ChatGPT web interface)

**Contrast with PASS:** If API key was instant, Docker/Redis were truly optional (degraded mode), and demo showed unique feature (searches your local files while chatting), could hit 10 min with clear value.

---

### FAIL Example 2: No Aha Moment

**Project type:** Python library for data validation

**Scenario:** Clean README, simple `pip install`, runs in 3 minutes total.

**Timing breakdown:**
1. **Discovery:** 0 min
2. **Comprehension:** 1 min
3. **Installation:** 1 min (`pip install`)
4. **First Use:** 1 min (import works, no errors)
5. **Aha Moment:** NONE - just "import succeeded"
   - README has examples but they just show syntax
   - No demo data to validate
   - No clear "before/after" showing what problem it solves
   - **Total: 3 minutes but no value demonstrated**

**Why this FAILS:**
1. Time is excellent (3 min) but fails on aha moment requirement
2. Successfully importing a library is not value - it's setup completion
3. User doesn't see what problem this solves or why it's better than alternatives
4. Would need to read docs, create test data, write validation rules to see any value (>10 additional minutes)

**Contrast with PASS:** If README included a `demo.py` that validates sample data and shows colored output with clear errors caught → aha moment in <5 minutes total.

---

### PASS Example for Contrast: Quick Value Demonstration

**Project type:** CLI tool for analyzing bundle size

**Timing breakdown:**
1. **Discovery:** 0 min
2. **Comprehension:** 1 min (README shows example output)
3. **Installation:** 1 min (`npm install -g`)
4. **First Use:** 30 sec (run on sample project)
5. **Aha Moment:** Immediate - shows pie chart of bundle breakdown, highlights largest dependency (lodash = 60% of bundle!)
   - **Total: 2.5 minutes with clear actionable insight**

✅ Passes: Under 10 min with tangible value (now knows what to optimize)

---

## Gate 4: The Bartender Test

### FAIL Example 1: Pure Technical Jargon

**Project type:** Distributed caching system

**Attempted bartender sentence:**
"A distributed caching layer with eventual consistency guarantees for microservice architectures"

**Why this FAILS:**
1. ❌ **Jargon check:** "distributed", "caching layer", "eventual consistency", "microservice architectures" - ALL jargon
2. ❌ **Value focus:** Describes WHAT it is (architecture), not what value it provides
3. ✅ **Single sentence:** Yes (one sentence)
4. ❌ **No questions:** Bartender would ask "what's a microservice?" and "what does that do for me?"

**Checklist: 1/4 checks passed → FAIL**

**Contrast with PASS:** "It remembers your data so your website loads faster for everyone" → zero jargon, value-focused (faster loading), clear outcome.

---

### FAIL Example 2: Implementation Over Value

**Project type:** Developer tool for deployment automation

**Attempted bartender sentence:**
"It helps developers by providing a CLI for their deployment pipeline automation"

**Why this FAILS:**
1. ❌ **Jargon check:** "CLI", "deployment pipeline" - jargon (bartender doesn't know these terms)
2. ❌ **Value focus:** Describes HOW (provides CLI, automates pipeline) not WHAT VALUE (what improves for users?)
3. ✅ **Single sentence:** Yes
4. ❌ **No questions:** Bartender asks "what's a CLI?" and "what do you mean by deployment?"

**Checklist: 1/4 checks passed → FAIL**

**Contrast with PASS:** "It publishes your work to the internet with one button" → zero jargon, outcome-focused (work goes live), immediate comprehension.

---

### FAIL Example 3: Multiple Sentences

**Project type:** Markdown presentation tool

**Attempted bartender explanation:**
"You write slides in a simple text format. It turns them into a beautiful presentation. You can share it as a website."

**Why this FAILS:**
1. ✅ **Jargon check:** No jargon (text, slides, presentation, website are all common terms)
2. ✅ **Value focus:** Describes outcome (beautiful presentation, shareable)
3. ❌ **Single sentence:** THREE separate sentences with independent subjects → fails
4. ✅ **No questions:** Clear and understandable

**Checklist: 3/4 checks passed → FAIL** (must pass ALL checks)

**Contrast with PASS:** "You describe what you want and it makes you a presentation you own forever" → same value, one sentence (with compound clause).

---

### PASS Example for Contrast: Clear Value Statement

**Project type:** File conversion tool

**Bartender sentence:**
"It turns your documents into PDFs that look good and work everywhere"

**Checklist:**
1. ✅ **Jargon check:** Zero jargon (documents, PDFs, look good, work are all common)
2. ✅ **Value focus:** Outcome-focused (looks good, works everywhere) not implementation
3. ✅ **Single sentence:** One sentence with compound clause
4. ✅ **No questions:** Immediate understanding, no follow-up needed

✅ Passes: 4/4 checks → clear value, no jargon, single sentence

---

## Gate 5: The Wallet Test

### FAIL Example 1: Can Only Name Archetypes, Not Real People

**Project type:** Task management CLI

**Scenario:** A command-line tool for managing tasks across multiple projects with tags and priorities.

**Attempted wallet test:**
- Question 1: Would you pay $20/month? → "Yes, I find it very useful"
- Question 2: Name 3 specific people who would pay → "Developers would pay for this. Also project managers need this. Content creators would find it valuable."

**Why this FAILS:**
1. Named archetypes ("developers", "project managers", "content creators") not real people
2. Cannot message these people today to validate their problem
3. Fails "real names" validation: These are hypothetical personas, not confirmed users
4. Self-audit signals: Likely only 1-2 of 4 signals = Yes (insufficient for pass)

**Contrast with PASS:** "John Smith from my team complained about task tracking last week, Sarah Johnson asked me for the tool link, and Mike Chen from the adjacent team said he'd switch from Todoist" → Real names of people you could message today who you've confirmed have this problem.

---

### FAIL Example 2: Self-Audit - Wouldn't Pay Because Already Built It

**Project type:** Personal automation script

**Scenario:** A script that automates file organization based on custom rules.

**Attempted wallet test:**
- Question 1: Would you pay $20/month? → "Well, I wouldn't pay because I already have it. But if I didn't build it, maybe?"
- Question 2: Name 3 specific people → Can name 2 real people who might use it

**Self-audit signal check:**
- Signal A (paid for similar): "No, I've never paid for file organization tools"
- Signal B (3 real names): "No, can only name 2 people"
- Signal C (5+ conversations): "No, haven't validated with anyone"
- Signal D (comparable tools charge $10-100): "No, most file org tools are free"
- **Total: 0 of 4 signals = Yes**

**Why this FAILS:**
1. Hedging on payment question reveals true valuation is <$20/month
2. Revealed preference (never paid for similar tools) shows low willingness to pay
3. 0 signals = Yes → Clear FAIL threshold
4. Counterfactual test would also fail: Can't name specific subscription to cancel

**Contrast with PASS:** "I currently pay $15/month for Hazel and $10/month for Dropbox automations. If I hadn't built this, I would absolutely pay $20/month - it combines both tools and adds custom rules they don't support. I'd cancel Hazel to afford this." → Clear willingness to pay demonstrated.

---

### FAIL Example 3: Third-Party Audit - No Market Validation Signals

**Project type:** GitHub repo with 12 stars, no issues, no production users

**Scenario:** An open-source library for parsing configuration files.

**Third-party audit signal check:**
- Signal A (stars >100 OR features requested >10): "No" - 12 stars, 0 feature requests
- Signal B (comparable paid tools exist): "No" - all config parsers are free/open-source
- Signal C (solves problem that costs time/money): "Maybe" - hard to quantify time saved
- Signal D (3+ production users reported): "No" - no issues, PRs, or discussions mentioning production use

**Total: 0 of 4 signals clearly = Yes** (Signal C is weak "maybe")

**Why this FAILS:**
1. Low GitHub engagement (12 stars) suggests limited real-world adoption
2. Zero feature requests indicates no one cares enough to ask for improvements
3. No production usage evidence means no validation of real-world value
4. Comparable paid tools don't exist because there's no willingness to pay in this category

**Contrast with PASS:** 150 stars + 15 feature request issues + 3 users commenting "we use this in production for X" + comparable commercial config management tools charge $50-200/month = 4/4 signals → Clear PASS.

---

## Gate 6: The Job-to-be-Done

### FAIL Example 1: Vague Situation

**Project type:** Productivity dashboard

**Scenario:** A web app showing various productivity metrics and recommendations.

**Attempted JTBD:**
"When I need to be more productive, I want to track my habits, so I can improve my workflow"

**JTBD checklist evaluation:**
- [ ] Situation is specific → **NO** - "need to be productive" is not observable, no when/where/context
- [ ] Motivation is action-oriented → **YES** - "track my habits" is a verb phrase
- [ ] Outcome is tangible → **NO** - "improve my workflow" is vague, not measurable
- [ ] Single job → **YES** - one job (habit tracking)

**Score: 2 of 4 = Yes → FAIL**

**Why this FAILS:**
1. "Need to be productive" is a feeling, not a situation you could observe
2. Can't answer: When exactly does this situation occur? Monday morning? End of day? During work?
3. "Improve my workflow" is not verifiable - how would you know if workflow improved?
4. Specificity test fails: Can't observe someone "needing to be productive"

**Contrast with PASS:** "When I'm reviewing my weekly tasks on Monday morning, I want to identify which habits correlated with completed goals, so I can prioritize those habits this week" → Specific situation (Monday morning review), observable context, measurable outcome (identify correlations, prioritize specific habits).

---

### FAIL Example 2: Multiple Unrelated Jobs

**Project type:** All-in-one business tool

**Scenario:** A platform combining time tracking, invoicing, scheduling, and client communication.

**Attempted JTBD:**
"When I need to run my business, I want to track time AND manage invoices AND schedule meetings AND communicate with clients, so I can stay organized"

**JTBD checklist evaluation:**
- [ ] Situation is specific → **NO** - "run my business" is too broad, always occurring
- [ ] Motivation is action-oriented → **NO** - Four separate actions combined (not a single motivation)
- [ ] Outcome is tangible → **NO** - "stay organized" is vague
- [ ] Single job → **NO** - Four unrelated jobs crammed together (time tracking ≠ invoicing ≠ scheduling ≠ communication)

**Score: 0 of 4 = Yes → FAIL**

**Why this FAILS:**
1. Combines four separate jobs that users hire different tools for
2. Time tracking job: "When finishing a client project, I want to calculate billable hours, so I can invoice accurately"
3. Invoicing job: "When project completes, I want to generate professional invoice, so I get paid quickly"
4. Scheduling job: "When client requests meeting, I want to show availability, so we book without email tennis"
5. Each deserves its own JTBD - cramming them together obscures the primary job

**Contrast with PASS:** Pick ONE primary job and treat others as supporting features. If time tracking is primary: "When I complete work for a client, I want to log billable hours without manual entry, so I can invoice accurately and get paid faster" → Then invoicing becomes a natural extension of time tracking, not a separate job.

---

### PASS Example for Contrast: Clear Situation-Motivation-Outcome

**Project type:** Markdown presentation tool

**JTBD:**
"When I need to present an idea to stakeholders, I want to create a professional deck without learning design tools, so I can focus on my message not the medium"

**JTBD checklist evaluation:**
- [x] Situation is specific → **YES** - "present to stakeholders" is observable context
- [x] Motivation is action-oriented → **YES** - "create a professional deck" is concrete verb
- [x] Outcome is tangible → **YES** - "focus on message not medium" is verifiable (time spent on content vs. design)
- [x] Single job → **YES** - one primary job (create presentation)

**Score: 4 of 4 = Yes → PASS**

---

## Gate 7: The Friction Audit

### FAIL Example 1: High Friction Installation

**Project type:** Local development environment tool

**Scenario:** A tool that requires Docker, Redis, PostgreSQL, and 3 API key signups before first use.

**Friction scoring:**

| Step | Score | Time/Measure | Issue |
|------|-------|--------------|-------|
| Discovery | Low (1) | 1 min | Found via GitHub search |
| Comprehension | Medium (2) | 3 min | README clear but long |
| Installation | High (3) | 25 min | Docker install (10m) + Redis (5m) + PostgreSQL (5m) + API keys (5m waiting) |
| First Use | High (3) | 15 min | Configuration errors, had to debug .env file |
| Repeated Use | Medium (2) | 5 steps | Must start Docker, Redis, Postgres each time |
| Mastery | Medium (2) | 45 min | Many features, complex interactions |

**Total friction score: 13** (threshold for pass is ≤9)

**Why this FAILS:**
1. Total score 13 > pass threshold of 9
2. Installation alone scored High (3) with 25 minutes required
3. First Use also High (3) due to configuration debugging
4. Multiple blocking steps (Docker, Redis, Postgres are project-specific, not domain-standard)
5. Even with borderline check (10-12 range), score of 13 is clear FAIL
6. Value delivered doesn't justify >40 minutes before first success

**Contrast with PASS:** If tool provided Docker Compose one-command setup (3 minutes) and included working .env.example (no debugging needed), Installation and First Use would both drop to Low (1), bringing total to 8 → PASS.

---

### FAIL Example 2: Hidden Friction in Repeated Use

**Project type:** Deployment automation tool

**Scenario:** Works great first time, but requires manual configuration file editing for each subsequent deployment.

**Friction scoring:**

| Step | Score | Time/Measure | Issue |
|------|-------|--------------|-------|
| Discovery | Low (1) | 1 min | Good SEO, found easily |
| Comprehension | Low (1) | 1 min | Clear value proposition |
| Installation | Medium (2) | 5 min | Standard npm install + config |
| First Use | Medium (2) | 8 min | Worked but needed config tweaking |
| Repeated Use | High (3) | 12 steps | Must manually edit config.json for each deploy (target, environment, credentials), no CLI flags |
| Mastery | High (3) | 90 min | Advanced features poorly documented |

**Total friction score: 12** (borderline range 10-12)

**Borderline check:** Is highest friction step (Repeated Use = High) justified by value?
- **NO** - Users won't adopt a deployment tool that requires 12 manual steps per deploy
- Defeats the purpose of "automation" if repeated use is more painful than alternatives
- Config editing is error-prone (credentials, typos)

**Verdict: FAIL** (borderline check failed)

**Why this FAILS:**
1. Repeated Use scored High (3) - the most critical step for adoption
2. 12 manual steps per deployment is unacceptable for an "automation" tool
3. Alternatives (GitHub Actions, Vercel CLI) have ≤3 steps for repeated use
4. Borderline test: High friction step NOT justified by value (automation should reduce steps, not add them)
5. Users will abandon tool after first use when they discover repeated friction

**Contrast with PASS:** If tool saved config profiles and accepted CLI flags (`deploy --profile production`), Repeated Use would drop to Low (1 step: run command), bringing total to 8 and passing borderline check → PASS.

---

### PASS Example for Contrast: Low Friction Throughout

**Project type:** CLI tool for code formatting

**Friction scoring:**

| Step | Score | Time/Measure | Issue |
|------|-------|--------------|-------|
| Discovery | Low (1) | 30 sec | npm search results |
| Comprehension | Low (1) | 1 min | README shows before/after |
| Installation | Low (1) | 2 min | `npm install -g` |
| First Use | Low (1) | 30 sec | Run on sample file, see formatted output |
| Repeated Use | Low (1) | 1 step | Single command: `format myfile.js` |
| Mastery | Medium (2) | 45 min | Advanced options available |

**Total friction score: 7** (well under ≤9 threshold)

✅ Passes: Low friction throughout user journey, no blocking steps, immediate value

---

## Gate 8: The Second-Order Consequences

### FAIL Example 1: Creates Worse Lock-In Than Problem It Solves

**Project type:** Data migration tool

**Scenario:** A tool that migrates data between platforms but uses a proprietary intermediate format for storage.

**Second-order analysis:**

**Positive consequences:**
- Would tell others (viral): +0 - Migration is one-time, no ongoing shareability
- Would build on it (ecosystem): +0 - No API or extension points
- Would return (retention): +1 - Might use again for future migrations

**Negative consequences:**
- Lock-in: -1 - Data stored in proprietary format, harder to move than original platform
- Maintenance burden: -1 - Must maintain intermediate storage, backup strategy
- New dependency: -1 - Created reliance on this tool's format that didn't exist before

**Net score: (+1) + (-1 -1 -1) = -2**

**Why this FAILS:**
1. Net score -2 < -1 threshold (clear FAIL)
2. Solves migration problem by creating NEW migration problem (locked into proprietary format)
3. More negative consequences (-3) than positive (+1)
4. Lock-in is WORSE than original problem: At least original platform was documented/supported
5. Users now stuck maintaining intermediate storage layer forever

**Contrast with PASS:** If tool used standard intermediate format (JSON, CSV) or direct platform-to-platform transfer (no intermediate storage), Lock-in would be 0 or even positive (easier to move), changing net to +1 → PASS.

---

### FAIL Example 2: One-Time Use, No Retention

**Project type:** Code formatter for legacy codebases

**Scenario:** A tool that formats old code to modern standards. Run once, then never needed again.

**Second-order analysis:**

**Positive consequences:**
- Would tell others (viral): +0 - Not shareable (legacy codebase is private, use case is one-time)
- Would build on it (ecosystem): +0 - No reason to extend a one-time formatter
- Would return (retention): +0 - Codebase is now formatted, never run again

**Negative consequences:**
- Lock-in: +0 - No lock-in (formats to standard)
- Maintenance burden: +0 - No ongoing maintenance
- New dependency: +0 - No dependency after one-time use

**Net score: 0 (no positive or negative ongoing consequences)**

**Borderline check:** Are positives high-impact and negatives low-impact?
- Positives: None ongoing (one-time use)
- Negatives: None significant
- **Verdict: NO** - No high-impact positives to offset zero ongoing value

**Why this FAILS:**
1. Net score 0 → Triggers borderline check
2. Borderline check fails: No high-impact positives
3. Zero retention means no compound value over time
4. No viral potential (can't share one-time use case)
5. No ecosystem potential (why build on one-time tool?)
6. Utility ends after first use

**Contrast with PASS:** If tool also provided ongoing linting/formatting enforcement (runs on git commit hook, catches new code), retention would be +1 (ongoing value) and net would be +1 → PASS. Or if it had strong shareability (standardized company-wide formatting rules), viral could be +1 → PASS.

---

### PASS Example for Contrast: Strong Positive Second-Order Effects

**Project type:** API client library with plugin system

**Second-order analysis:**

**Positive consequences:**
- Would tell others (viral): +1 - Simple to explain, solves common pain, developers recommend to teammates
- Would build on it (ecosystem): +1 - Plugin API allows community extensions
- Would return (retention): +1 - Ongoing API calls mean repeated use

**Negative consequences:**
- Lock-in: -1 - Some coupling to library's abstractions (but standard HTTP underneath)
- Maintenance burden: +0 - Self-updating, minimal maintenance needed
- New dependency: +0 - Replaces direct HTTP calls (wash, not net new)

**Net score: (+1 +1 +1) + (-1) = +2**

✅ Passes: Net +2 ≥ 1 threshold, more positive than negative consequences

---

## Gate 9: Occam's Razor

### Pass Examples
- TBD

### Fail Examples
- TBD

---

## Gate 10: The Pareto Gate

### Pass Examples
- TBD

### Fail Examples
- TBD

---

## Gate 11: Regret Minimization

### Pass Examples
- TBD

### Fail Examples
- TBD

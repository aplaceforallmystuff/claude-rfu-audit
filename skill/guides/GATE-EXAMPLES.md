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

### Pass Examples
- TBD

### Fail Examples
- TBD

---

## Gate 6: The Job-to-be-Done

### Pass Examples
- TBD

### Fail Examples
- TBD

---

## Gate 7: The Friction Audit

### Pass Examples
- TBD

### Fail Examples
- TBD

---

## Gate 8: The Second-Order Consequences

### Pass Examples
- TBD

### Fail Examples
- TBD

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

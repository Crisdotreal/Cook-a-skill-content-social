# 📜 PRODUCT SPECIFICATION: SOCIAL-CONTENT-SNIPER

> **Version:** 1.0 (MVP)
> **Status:** Ready for Development
> **Objective:** To empower Content Teams with an AI-driven workflow that identifies trends, filters for brand relevance, and generates actionable content in real-time.

---

## 1. Problem Statement

Content Teams are currently facing three major challenges that reduce overall productivity:

| # | Challenge | Description |
|---|-----------|-------------|
| 1 | **High Latency** | Trends are discovered too late, after they have already peaked and saturated. |
| 2 | **Relevance Gap** | Difficulty connecting trends to the brand's identity (Brand DNA), resulting in forced or off-brand content. |
| 3 | **Passive Execution** | Focusing only on publishing to owned channels (Owned Media) while missing opportunities to engage in high-velocity conversations happening across social media. |

**Goal:** Transform the manual research process into a proactive **Active Growth Loop**.

---

## 2. Target User

The term "Content Team" in this spec refers specifically to the following persona:

| Attribute | Description |
|-----------|-------------|
| **Primary Role** | Content Writer or Social Media Manager embedded in a crypto / Web3 project. |
| **Team Size** | Solo operator or small team of 2–3 people without a dedicated research function. |
| **Workload** | Manages 2–3 social accounts simultaneously; expected to publish 3–5 trend-driven posts per day. |
| **Core Pain** | Spends 1–2 hours daily on manual trend research across X/Twitter, news sites, and Telegram — time that should go toward writing. |
| **Technical Level** | Non-developer. Comfortable with Markdown files and chat interfaces, but does not write code. |
| **Success Metric** | A post that generates meaningful engagement (replies, quotes, reshares) within the first 2 hours of publishing. |

**Secondary users** who may interact with outputs (but are not the primary operator):

- **Marketing Lead** — reviews the Daily Brief before approving posts.
- **Community Manager** — executes the Reply Strategy in live threads.

---

## 3. Solution Overview

**Trend-Sniper** is an AI Skill that operates as a 24/7 market research specialist, running in three sequential steps:

```
[ SCAN ] ──▶ [ FILTER ] ──▶ [ GENERATE ]
```

1. **Scan:** Crawl multiple news sources and social media signals simultaneously.
2. **Filter:** Apply an intelligent scoring system to surface only the trends that align with the brand.
3. **Generate:** Produce a complete content strategy, including ready-to-post drafts and a Reply Strategy for community engagement.

---

## 4. Input Requirements

### 4.1 Product Spec File (Primary Input)

The primary input is the brand's **`product_spec.md`** file. This file is the single source of truth — brand context is embedded directly into the spec, eliminating the need for a separate configuration file.

**Required structure inside `product_spec.md`:**

```markdown
## Brand Context

- **brand_voice**       : [Brand persona — e.g., Degen, Expert, Witty, Professional]
- **niche_keywords**    : [Core topic list — e.g., "AI Agents", "Layer 2", "DeFi"]
- **target_audience**   : [Detailed persona of the ideal reader]
- **value_proposition** : [The core problem the brand solves]
- **blacklist**         : [Topics to avoid entirely — e.g., Politics, NSFW]
```

The Skill reads this file at the start of each session and extracts these fields as parameters for the entire pipeline. For missing fields, see **Section 6 — Edge Cases, Case 2**.

| Field | Role in Pipeline |
|-------|-----------------|
| `brand_voice` | Sets the tone for all generated content drafts. |
| `niche_keywords` | Seed queries for the Multi-Source Scanning step. |
| `target_audience` | Determines the content angle for each trend. |
| `value_proposition` | Used to bridge into the brand in Reply Strategy (Soft Plug). |
| `blacklist` | Safety filter applied before any content is generated. |

### 4.2 Session Goal — Clarifying Question

Before execution, the AI will ask the user to define the session's objective:

> **"What is your focus for today?"**
>
> - **(A) News Hunting** — Breaking news & updates. Prioritizes the freshest trends; output focuses on Content Drafts.
> - **(B) Community Engagement** — High-value replies & discussions. Prioritizes Reply Strategy; scans for actively trending threads within the niche.
> - **(C) Evergreen Only** — Educational & Deep Dive content. No trend dependency. Use this when the market is slow or you want to publish foundational content (e.g., "Back to Basics", "Thesis breakdown") based on `niche_keywords`.

> ⚠️ **MVP Note:** Sentiment Analysis (Fear/Greed) is excluded from v1.0 — output format is not yet defined. Scheduled for design in v1.1.

---

## 5. Processing Logic

### Step 1: Multi-Source Scanning

Using the brand's `niche_keywords` as seed queries, the AI runs searches across three source categories:

- **Google News (Last 24h)** — for breaking news and editorial coverage.
- **Social Media Discussions** — posts and threads with high engagement signals (likes, shares, comments).
- **Niche Forums** — relevant communities on Reddit or specialized platforms where the target audience is active.

All retrieved results are aggregated and passed to the scoring step.

### Step 2: Relevance Scoring System

Each discovered trend is scored on a **scale of 10 points** across three dimensions. Scores are calculated deterministically using the rubric below — no subjective judgment.

---

#### 🌡️ Dimension 1: Hotness Score — *Max 3 pts*

Measures recency. A trend that has already saturated offers no first-mover advantage.

| Time Since Published | Score |
|----------------------|-------|
| < 4 hours ago | **3 pts** |
| 4 – 12 hours ago | **2 pts** |
| 12 – 24 hours ago | **1 pt** |
| > 24 hours ago | **0 pts** *(auto-disqualified)* |

---

#### 🎯 Dimension 2: Brand Fit Score — *Max 4 pts*

Measures how naturally the brand can insert itself into the conversation. Each sub-criterion is evaluated independently against the `Brand Context` section of `product_spec.md`.

| Sub-Criterion | Points | Pass Condition |
|---------------|--------|----------------|
| **Keyword Match** | +2 pts | Trend contains ≥ 1 keyword from `niche_keywords`. |
| **Audience Match** | +1 pt | Topic directly impacts the `target_audience`'s finances or workflow. |
| **Tone Match** | +1 pt | Trend can be written in the defined `brand_voice` *(e.g., a humorous voice cannot engage with a tragedy)*. |

---

#### 🔥 Dimension 3: Viral Potential — *Max 3 pts*

Measures the trend's inherent ability to generate organic amplification.

| Sub-Criterion | Points | Pass Condition |
|---------------|--------|----------------|
| **Engagement Signal** | +1.5 pts | Source post has >100 *substantive* replies (excludes GM spam, seeding comments, or single-word reactions). |
| **Debate Factor** | +1.5 pts | Topic has ≥ 2 clearly opposing camps of opinion — controversy drives reshares. |

---

#### 🔴 Scoring Threshold & Decision Logic

```
Total Score = Hotness + Brand Fit + Viral Potential  (Max: 10)

>= 7  →  ✅ PRIMARY TREND   — Enter content generation pipeline.
5 – 6 →  🟡 BACKUP TREND    — Queue for later or low-effort formats.
< 5   →  ❌ DISCARD         — Skip entirely.
```

### Step 3: Content Angle Development

For each selected trend, the AI develops **3 distinct content angles**:

1. **The "News" Angle** — Informativeness & Insight.
2. **The "Relatable" Angle** — Empathy & Humor (Meme).
3. **The "Contrarian" Angle** — Counter-intuitive opinion.

---

## 6. Edge Cases — Exception Handling Logic

Four exception scenarios can occur within the pipeline. Each case has a clearly defined **trigger condition** and **required action** — the Skill must never fail silently or self-handle without notifying the user.

---

### Case 1: No Trend Scores ≥ 7

**Trigger:** All scan results score below 7 after the scoring step.

```
IF no_trend.score >= 7 THEN
  → Activate "Evergreen Content" mode
  → Notify user: "No high-scoring trends found today.
     Recommendation: Publish Evergreen content (Educational post / Niche Meme)."
  → Generate 1 draft content on a "Back to Basics" topic, seeded from niche_keywords
  → Tag output: [EVERGREEN] instead of [TREND]
```

> **Rationale:** A day without strong trends still requires output. Evergreen content keeps the publishing schedule intact without forcing awkward, low-relevance trend-jacking.

---

### Case 2: `product_spec.md` is Missing a Required Field

**Trigger:** One or more fields in the `Brand Context` section of the spec file are absent or blank.

```
IF product_spec.brand_context.field == NULL THEN
  → Apply Smart Default for that field (see table below)
  → Prepend warning to output:
    "⚠️ Warning: [field_name] is missing from product_spec.md.
     Applying default value. Update the spec file for more accurate results."
  → Continue pipeline (do NOT halt)
```

| Missing Field | Smart Default Applied |
|---------------|-----------------------|
| `blacklist` | Auto-block: **Sex, Politics, Violence** |
| `brand_voice` | Fallback to: **"Professional & Informative"** |
| `target_audience` | Infer audience from `niche_keywords` |
| `value_proposition` | Skip the "Soft Plug" step in Reply Strategy |

---

### Case 3: Trend Appears on Only One Source (Unverified)

**Trigger:** After scanning, a trend has only one source — not yet confirmed by major outlets or reputable KOLs.

```
IF trend.source_count == 1 THEN
  → Prepend label [UNVERIFIED] to the draft content
  → Source link is mandatory in the output
  → Add disclaimer:
    "⚠️ Single source only. Verify before publishing."
  → Still generate the draft (do not discard) — leave the final call to the user
```

> **Rationale:** Breaking news often starts with a single source and still turns out to be real. Rather than discarding it, label it clearly so the Content Team can self-verify — avoiding missed scoops.

---

### Case 4: Sensitive Trend Not in `blacklist`

**Trigger:** A trend contains keywords from the built-in sensitive list, even if the user has not explicitly blacklisted them.

```
Built-in sensitive keyword list:
  → Names of politicians / political parties
  → War / conflict keywords (war, attack, invasion)
  → Strong negative keywords (arrested, died, scam, fraud, bankrupt)

IF trend.keyword ∈ sensitive_list THEN
  → Flag trend as [SENSITIVE]
  → Do NOT generate content immediately
  → Display confirmation prompt:
    "🔴 Sensitive Trend Detected: '[Trend Name]'
     This topic may carry reputational risk for the brand.
     Do you want to proceed with content generation? (Yes / No / More Info)"
  → IF user = Yes → Generate with disclaimer [USE WITH CAUTION]
  → IF user = No  → Discard, move to next trend
```

> **Rationale:** A user-defined blacklist cannot cover every scenario. The built-in safety filter acts as a final checkpoint before sensitive content is generated.

---

### Summary: Decision Flow — Edge Case Handler

```
START PIPELINE
    │
    ├─ product_spec.md Brand Context complete? ──No──▶ [Case 2] Apply Smart Default + Warn
    │         │ Yes
    ▼
  SCAN & SCORE
    │
    ├─ Any trend >= 7 pts? ──No──▶ [Case 1] Evergreen Mode
    │         │ Yes
    ▼
  FOR EACH qualified trend:
    │
    ├─ Source count == 1? ──Yes──▶ [Case 3] Tag [UNVERIFIED]
    │
    ├─ Sensitive keyword? ──Yes──▶ [Case 4] Confirm prompt → User decides
    │
    ▼
  GENERATE CONTENT → OUTPUT
```

---

## 7. Output Deliverable

The system returns a Markdown report: **`DAILY_BRIEF_[DATE].md`**

Output is split into two main parts: **Content Drafts** for owned channels and **Reply Strategy** for community engagement. Every qualified trend will always include both.

---

### Full Example Output

> **Scenario:** Input is the `product_spec.md` of a Crypto KOL with `brand_voice: Degen/Witty`. Event: Vitalik Buterin just posted a tweet about AI Agents on Ethereum.

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
📋 DAILY TREND BRIEF
Date   : Feb 11, 2026
Focus  : Breaking News
Status : ✅ Found 1 High-Score Trend
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

🚨 TREND #1: Vitalik & The Agent Economy
─────────────────────────────────────────
Score  : 9.5/10
         ├─ Hotness      : 3.0 / 3   (Published < 2h ago)
         ├─ Brand Fit    : 4.0 / 4   (Keyword ✅ | Audience ✅ | Tone ✅)
         └─ Viral Pot.   : 2.5 / 3   (Engagement ✅ | Debate: Partial)

Source : https://x.com/VitalikButerin/status/2021594878162157948
Summary: Vitalik states Ethereum will be the home for AI Agents.
         13,000 agents registered on-chain in a single day.


━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
🏗️  PART 1 — CONTENT DRAFTS (Pick 1)
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

┌─ OPTION A: The "Degen" Angle (Humor / FOMO) ──────────────────┐

Hook:
"Retail is selling ETH to pay for weddings.
Meanwhile Vitalik is building a kingdom for AI." 🤖🏠

Body:
13,000 AI agents just migrated on-chain yesterday.
They don't sleep. They don't take bathroom breaks.
And most importantly — they don't panic sell on FUD.

CTA:
"Are your bags loaded with AI gems yet,
or are you still fully sidelined in stables? 👇 #ETH #AI"

└────────────────────────────────────────────────────────────────┘

┌─ OPTION B: The "Insight" Angle (Expert / Serious) ────────────┐

Hook:
"The next 1 billion users on Ethereum won't be humans." 🧠

Body:
Vitalik confirmed it. The Agent Economy is scaling faster
than DeFi did in 2020.

If you're ignoring infrastructure for AI Agents, you're
trading 2024 tech with a 2017 mindset.

CTA:
"Infrastructure > Memes. Change my mind. 👇"

└────────────────────────────────────────────────────────────────┘


━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
💬  PART 2 — REPLY STRATEGY (Engagement)
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

🎯 TARGET 1: Reply to Vitalik's original thread
   Strategy : Value Add — Inject a deeper insight into the main thread.
   ─────────────────────────────────────────────
   Draft Reply:
   "The most interesting part isn't just the agents —
   it's the on-chain verification of their outputs.
   Trustless compute is the missing piece."

🎯 TARGET 2: Reply to KOL complaining about speed/TPS
   Strategy : Counter-Argument — Polite pushback that positions expertise.
   ─────────────────────────────────────────────
   Draft Reply:
   "Speed doesn't matter if the agents can't verify the data.
   ETH L2s are solving the reliability layer, not just TPS."
```

---

## 8. Success Criteria

| # | Criterion | Target |
|---|-----------|--------|
| 1 | **Zero Hallucination** | 100% of content is grounded in real, verifiable facts with cited sources. |
| 2 | **Ready-to-Post** | Draft content meets 90% quality threshold, requiring minimal human editing. |
| 3 | **Speed** | The full pipeline (Scan → Draft) completes in the shortest time possible. |


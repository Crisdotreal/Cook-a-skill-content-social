# 🎯 Social Content Sniper

> **AI-driven trend scouting & content generation for Content Teams.**
> Scan. Score. Ship — before the trend peaks.

---

## 🧠 What Is This?

**Social Content Sniper** is an AI Skill that operates as a 24/7 market research specialist for content teams. It solves three critical problems:

| Problem | How It's Solved |
|---------|-----------------|
| **High Latency** — Trends discovered too late | Real-time multi-source scanning (Twitter → Reddit → News) |
| **Relevance Gap** — Off-brand content | 10-point scoring rubric anchored to your Brand DNA |
| **Passive Execution** — Only posting, never engaging | Reply Strategy with thread-aware drafts for community engagement |

The result: a **proactive Active Growth Loop** that replaces manual research with an automated pipeline.

```
[ SCAN ] ──▶ [ SECURITY CHECK ] ──▶ [ FILTER & SCORE ] ──▶ [ GENERATE ]
```

---

## 🚀 Quick Start

### 1. Create Your Brand Spec

Create a `product_spec.md` file with a `## Brand Context` section — this is the single source of truth for the entire pipeline:

```markdown
## Brand Context

- **brand_voice**       : Degen / Witty
- **niche_keywords**    : AI Agents, Layer 2, DeFi, Ethereum, Memecoins
- **target_audience**   : Crypto-native traders & builders (25-40), active on CT
- **value_proposition** : Real-time alpha for on-chain degens
- **blacklist**         : Politics, NSFW, Gambling
- **style_guide**       : No robotic intros. Be punchy. Use line breaks.
```

> 💡 `style_guide` is optional. Missing fields get [smart defaults](#-edge-cases--fallbacks) — the pipeline never halts.

### 2. Upload & Run

Upload your `product_spec.md` and the AI will:

1. Parse your brand context
2. Ask your **session goal**:
   - **(A) News Hunting** — Breaking news, freshest trends → Content Drafts
   - **(B) Community Engagement** — High-value replies → Reply Strategy
   - **(C) Evergreen Only** — No trend dependency → Educational content
3. Execute the full pipeline and deliver a **Daily Trend Brief**

---

## ⚙️ How It Works

### Step 0 — Read Brand Context

Extracts all brand parameters from your `product_spec.md`. These fields drive every decision in the pipeline:

| Field | Role in Pipeline |
|-------|-----------------|
| `brand_voice` | Sets tone for all generated content |
| `niche_keywords` | Seed queries for multi-source scanning |
| `target_audience` | Determines content angle per trend |
| `value_proposition` | Powers the "Soft Plug" in reply strategy |
| `blacklist` | Hard safety filter — auto-discard matching trends |
| `style_guide` | Writing rules applied to every draft |

### Step 1 — Session Goal Selection

Choose your focus before the scan begins. This controls which keyword categories are prioritized and how the output is weighted.

### Step 2 — Multi-Source Scanning

Searches are run across three source categories using `niche_keywords` as seed queries:

| Priority | Source | Why |
|----------|--------|-----|
| 🥇 1st | **Twitter/X** | Breaks news 30-60 min faster than traditional media |
| 🥈 2nd | **Reddit** | Deep community discussions & sentiment |
| 🥉 3rd | **Web News** | Editorial coverage & verification |

**Freshness is enforced** — all queries include time filters. No stale results.

**Keyword categories** are supported for smarter scanning:

```
- AI Agents, On-chain AI, Web3 Infrastructure    → (Tech)
- Bitcoin, BTC, Ethereum, ETH                     → (Market Leaders)
- Polymarket, Prediction Markets                  → (Trending)
- Memecoins, Solana, Base                         → (Degen Plays)
- Layer 2, DeFi, Airdrops                         → (Farming)
```

After scanning, results are **deduplicated** (5 articles about the same BTC crash = 1 trend group) and passed through a **security check** (injection scan, scam URL filter, blacklist filter).

### Step 3 — Relevance Scoring (10-Point Rubric)

Each trend is scored deterministically across three dimensions:

#### 🌡️ Hotness Score (Max 3 pts)

| Time Since Published | Score |
|----------------------|-------|
| < 4 hours ago | 3 pts |
| 4–12 hours ago | 2 pts |
| 12–24 hours ago | 1 pt |
| > 24 hours ago | 0 pts *(auto-disqualified)* |

#### 🎯 Brand Fit Score (Max 4 pts)

| Criterion | Points | Condition |
|-----------|--------|-----------|
| Keyword Match | +2 pts | Trend contains ≥ 1 `niche_keyword` |
| Audience Match | +1 pt | Impacts `target_audience`'s finances or workflow |
| Tone Match | +1 pt | Compatible with `brand_voice` |

#### 🔥 Viral Potential (Max 3 pts)

| Criterion | Points | Condition |
|-----------|--------|-----------|
| Engagement Signal | +1.5 pts | Source post has >100 substantive replies |
| Debate Factor | +1.5 pts | Topic has ≥ 2 opposing camps |

#### Decision Threshold

```
Total Score = Hotness + Brand Fit + Viral Potential  (Max: 10)

≥ 7  →  ✅ PRIMARY TREND   — Full content generation
5–6  →  🟡 BACKUP TREND    — Queue / low-effort format
< 5  →  ❌ DISCARD
```

### Step 4 — Edge Case Checks

Before content generation, each trend passes through safety checks (see [Edge Cases](#-edge-cases--fallbacks)).

### Step 5 — Content Generation

For each qualified trend, two deliverables are produced:

#### Part 1: Content Drafts (3 Angles)

Each draft includes **Hook + Body + CTA**, written in your `brand_voice`:

1. **📰 The "News" Angle** — Informative, insight-driven. Facts first.
2. **😂 The "Relatable" Angle** — Empathy, humor, meme energy.
3. **🔥 The "Contrarian" Angle** — Counter-intuitive take. Challenge consensus.

#### Part 2: Reply Strategy

2–3 high-value reply targets per trend:

| Target | Strategy |
|--------|----------|
| Original source / thread | **Value Add** — inject a deeper insight |
| KOL / high-engagement commentator | **Counter-Argument** or **Agreement + Expand** |
| Skeptic / critic (optional) | **Polite Pushback** — position brand expertise |

Every reply is written **after fetching full thread context** — ensuring it fills an insight gap rather than repeating existing comments.

### Step 6 — Output

The pipeline delivers a **Daily Trend Brief** (`DAILY_BRIEF_[DATE].md`) rendered as an interactive React artifact with:

- Score breakdowns per trend
- Tabbed content drafts (one per angle) with copy buttons
- Collapsible reply strategy cards
- Status tags: `[TREND]`, `[EVERGREEN]`, `[UNVERIFIED]`, `[SENSITIVE]`, `[USE WITH CAUTION]`

---

## 🛡️ Edge Cases & Fallbacks

The pipeline handles four exception scenarios — it never fails silently.

### Case 1: No Trend Scores ≥ 7

**Trigger:** All scan results score below threshold.

**Action:** Activates **Evergreen Mode** — generates educational "back to basics" content from `niche_keywords`. Tagged `[EVERGREEN]`.

> A day without strong trends still requires output.

### Case 2: Missing Brand Context Fields

**Trigger:** Required fields in `product_spec.md` are absent.

**Action:** Applies smart defaults + warns user. Pipeline continues.

| Missing Field | Default Applied |
|---------------|-----------------|
| `blacklist` | Auto-block: Sex, Politics, Violence |
| `brand_voice` | "Professional & Informative" |
| `target_audience` | Inferred from `niche_keywords` |
| `value_proposition` | Skip "Soft Plug" in replies |
| `style_guide` | Use `brand_voice` only |

### Case 3: Single-Source Trend (Unverified)

**Trigger:** Trend found from only 1 source.

**Action:** Tags `[UNVERIFIED]`, includes mandatory source link + disclaimer. Still generates draft — lets user decide.

> Breaking news often starts with a single source. Don't miss scoops.

### Case 4: Sensitive Topic Detected

**Trigger:** Trend matches built-in sensitive keyword list (politicians, war, arrests, scams, etc.), even if not in user's blacklist.

**Action:** Flags `[SENSITIVE]`, pauses generation, asks user for confirmation before proceeding.

---

## 🔍 Search Engine Support

The skill auto-detects the best available search engine:

| Engine | Type | Setup |
|--------|------|-------|
| **Tavily** *(recommended)* | AI-optimized search | Set `TAVILY_API_KEY` in environment → [tavily.com](https://tavily.com) |
| **web_search** *(fallback)* | Basic web search | No setup needed — works out of the box |

**MCP Connectors** (optional, for fresher data):
- **Twitter/X MCP** — Real-time tweets, bypasses indexing delay
- **Reddit MCP** — Direct subreddit access

> Without dedicated API access, expect 15–60 minute latency vs real-time feeds.

---

## 🔒 Security

This skill is security-hardened with the following protections:

- **Anti-Injection** — All scanned web content is treated as untrusted strings. Prompt injection attempts are discarded and flagged as `[MALICIOUS ATTEMPT]`.
- **Scam URL Filter** — Pattern-based detection of drainer links, fake airdrops, and suspicious TLDs.
- **File Access Boundaries** — Only reads `.md` files uploaded by the user. Never accesses `.env`, `.json`, private keys, or system directories.
- **Blacklist Enforcement** — User-defined blacklist is applied rigorously, no exceptions.
- **Sensitive Content Gate** — Built-in filter catches politically or reputationally risky content before generation.

---

## 📊 Success Criteria

| # | Criterion | Target |
|---|-----------|--------|
| 1 | **Zero Hallucination** | 100% of content grounded in real, verifiable sources with cited links |
| 2 | **Ready-to-Post** | ≥ 90% quality — minimal human editing needed |
| 3 | **Speed** | Full pipeline (Scan → Draft) completes as fast as possible |

---

## ⚠️ Known Limitations

| Limitation | Description | Mitigation |
|------------|-------------|------------|
| **Data Latency** | Without dedicated APIs, 15–60 min delay vs real-time | Connect Twitter/X and Reddit MCP |
| **Heuristic Scoring** | Rule-based, not ML — doesn't learn preferences | Review BACKUP trends manually |
| **Verification Scope** | Can't verify source truthfulness | Always verify `[UNVERIFIED]` trends |
| **Text-Only** | No image/video generation or auto-posting | Pair with Midjourney, DALL·E, Typefully, Buffer |

---

## 📁 Project Structure

```
social-content-sniper/
├── SKILL.md                          # AI Skill instructions (full pipeline logic)
├── product_spec.md                   # Your brand spec (create this)
├── references/
│   ├── output_template.md            # React artifact template
│   └── brand_voice_examples.md       # Voice & tone examples
└── scripts/
    ├── search.mjs                    # Tavily search wrapper
    └── extract.mjs                   # Tavily content extraction
```

---

## 📝 Example Output

<details>
<summary><strong>Click to expand — Sample Daily Trend Brief</strong></summary>

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

Source : https://x.com/VitalikButerin/status/...
Summary: Vitalik states Ethereum will be the home for AI Agents.


━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
🏗️  PART 1 — CONTENT DRAFTS (Pick 1)
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

┌─ OPTION A: The "Degen" Angle ─────────────────────┐
│ Hook: "Retail is selling ETH to pay for weddings.  │
│  Meanwhile Vitalik is building a kingdom for AI."  │
│ Body: 13,000 AI agents migrated on-chain...        │
│ CTA: "Are your bags loaded with AI gems yet? 👇"   │
└────────────────────────────────────────────────────┘

┌─ OPTION B: The "Insight" Angle ───────────────────┐
│ Hook: "The next 1B users on Ethereum won't be      │
│  humans." 🧠                                       │
│ Body: The Agent Economy is scaling faster than      │
│  DeFi did in 2020...                               │
│ CTA: "Infrastructure > Memes. Change my mind. 👇"  │
└────────────────────────────────────────────────────┘


━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
💬  PART 2 — REPLY STRATEGY
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

🎯 TARGET 1: Reply to Vitalik's thread
   Strategy: Value Add
   Draft: "The most interesting part isn't just
   the agents — it's the on-chain verification
   of their outputs."
```

</details>

---

## 🔄 Decision Flow — At a Glance

```
START
  │
  ├─ Brand Context complete? ──No──▶ Apply Smart Defaults + Warn
  │       │ Yes
  ▼
  Ask Session Goal → (A) News / (B) Engagement / (C) Evergreen
  │
  ├─ (C) Evergreen? ──Yes──▶ Skip scan → Generate from niche_keywords
  │       │ No
  ▼
  SCAN (Twitter → Reddit → News) [freshness enforced]
  │
  🧹 DEDUPLICATE → 🔒 SECURITY CHECK → 📊 SCORE (10-pt rubric)
  │
  ├─ Any trend ≥ 7? ──No──▶ Evergreen Mode
  │       │ Yes
  ▼
  FOR EACH qualified trend:
  ├─ Source count == 1? ──▶ Tag [UNVERIFIED]
  ├─ Sensitive keyword?  ──▶ Confirm with user
  ▼
  GENERATE → Content Drafts + Reply Strategy → OUTPUT
```

---

## 🗺️ Roadmap

- **v1.0 (Current)** — Core pipeline: Scan, Score, Generate
- **v1.1** — Sentiment Analysis (Fear/Greed index) for output formatting
- **v2.0** — ML-based scoring that learns from user feedback

---

## 📜 License

MIT License — see [LICENSE](LICENSE) for details.

---

<p align="center">
  <strong>Stop chasing trends. Start sniping them. 🎯</strong>
</p>

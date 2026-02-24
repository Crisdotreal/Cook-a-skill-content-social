---
name: social-content-sniper
description: >
  AI-driven trend scouting and content generation skill for Content Teams. Use this skill whenever the user
  wants to find trending topics and generate social media content, draft tweets/posts based on current news,
  create reply strategies for community engagement, scout for breaking news in a niche, generate content
  angles for trending topics, or build a daily content brief. Also trigger when the user mentions
  "trend hunting", "content sniper", "daily brief", "news hunting", "community engagement strategy",
  "reply strategy", "trend scan", "content angles", or uploads a product_spec.md / brand spec file and
  wants content generated from it. Works with Twitter/X, Reddit, and web sources. Covers crypto, AI,
  tech, DeFi, and any niche defined in the user's brand spec.
---

# Social Content Sniper

An AI skill that operates as a 24/7 trend scout and content strategist. It scans multiple sources,
scores trends for brand relevance, and generates ready-to-post content drafts plus reply strategies.

## Pipeline Overview

```
[ SCAN ] ──▶ [ FILTER & SCORE ] ──▶ [ GENERATE ]
```

1. **Scan** — Search across web news, Twitter/X, and Reddit using brand keywords
2. **Filter & Score** — Apply a 10-point scoring rubric to surface brand-relevant trends
3. **Generate** — Produce content drafts (3 angles) + reply strategy for each qualified trend

All output is in **English**, rendered as a React artifact in chat.

---

## Step 0: Read Brand Context

At the start of every session, read the user's `product_spec.md` file (uploaded or in `/mnt/user-data/uploads/`).

Extract these fields from the `## Brand Context` section:

| Field | Purpose |
|-------|---------|
| `brand_voice` | Sets tone for all drafts |
| `niche_keywords` | Seed queries for scanning |
| `target_audience` | Determines content angle |
| `value_proposition` | Used for "Soft Plug" in replies |
| `blacklist` | Safety filter — skip these topics entirely |
| `style_guide` *(optional)* | Writing rules applied to every draft (e.g., "No robotic intros", "Be punchy") |

### Missing Field Handling (Edge Case 2)

If any field is missing or blank, apply smart defaults **and warn the user**:

| Missing Field | Smart Default |
|---------------|---------------|
| `blacklist` | Auto-block: Sex, Politics, Violence |
| `brand_voice` | "Professional & Informative" |
| `target_audience` | Infer from `niche_keywords` |
| `value_proposition` | Skip "Soft Plug" in reply strategy |
| `style_guide` | No additional rules — use `brand_voice` only |

Prepend to output: `⚠️ Warning: [field_name] is missing. Applying default value.`

---

## Step 1: Ask Session Goal

Before scanning, present 3 options to the user:

- **(A) News Hunting** — Breaking news & updates. Prioritizes freshest trends. Output focuses on Content Drafts.
- **(B) Community Engagement** — High-value replies & discussions. Prioritizes Reply Strategy. Scans for actively trending threads.
- **(C) Evergreen Only** — No trend dependency. User picks a topic from niche_keywords. Generates educational / "back to basics" content.

If user picks **(C)**, skip scanning and scoring — go directly to content generation using a niche_keyword topic.

---

## Step 2: Multi-Source Scanning

Use `niche_keywords` as seed queries. Run searches across **three source categories**:

### Source 1: Web News (Last 24h)
Use `web_search` with each keyword + "news today" or "breaking". Use `web_fetch` to read full articles.

### Source 2: Twitter/X
If Twitter/X MCP connector is available, search for posts with high engagement matching niche_keywords.
If not available, suggest the user connect it, then fallback to `web_search` with `site:x.com` or `site:twitter.com`.

### Source 3: Reddit
If Reddit MCP connector is available, search relevant subreddits for hot threads.
If not available, suggest connecting it, then fallback to `web_search` with `site:reddit.com`.

**Important**: For each source, capture:
- Title / headline
- URL / source link
- Approximate publish time (for Hotness scoring)
- Engagement signals (likes, comments, shares) when available
- A brief summary

Aggregate all results and pass to scoring.

---

## Step 3: Relevance Scoring

Score each trend on a **10-point scale** across three dimensions. Apply scores deterministically using this rubric:

### Dimension 1: Hotness Score (Max 3 pts)

| Time Since Published | Score |
|----------------------|-------|
| < 4 hours ago | 3 pts |
| 4–12 hours ago | 2 pts |
| 12–24 hours ago | 1 pt |
| > 24 hours ago | 0 pts → **auto-disqualified** |

### Dimension 2: Brand Fit Score (Max 4 pts)

| Sub-Criterion | Points | Condition |
|---------------|--------|-----------|
| Keyword Match | +2 pts | Trend contains ≥ 1 keyword from `niche_keywords` |
| Audience Match | +1 pt | Topic impacts `target_audience`'s finances or workflow |
| Tone Match | +1 pt | Topic can be written in the `brand_voice` |

### Dimension 3: Viral Potential (Max 3 pts)

| Sub-Criterion | Points | Condition |
|---------------|--------|-----------|
| Engagement Signal | +1.5 pts | Source post has >100 substantive replies |
| Debate Factor | +1.5 pts | Topic has ≥ 2 clearly opposing opinions |

### Decision Threshold

```
Total = Hotness + Brand Fit + Viral Potential  (Max 10)

≥ 7  →  ✅ PRIMARY TREND — Generate full content
5–6  →  🟡 BACKUP TREND — Queue / low-effort format
< 5  →  ❌ DISCARD
```

---

## Step 4: Edge Case Checks

Before generating content for each qualified trend, run these checks **in order**:

### Check A: No Trend ≥ 7 (Edge Case 1 — Evergreen Mode)
If no trend scores ≥ 7:
- Notify user: "No high-scoring trends found. Recommending Evergreen content."
- Generate 1 educational / "back to basics" draft from `niche_keywords`
- Tag output as `[EVERGREEN]`

### Check B: Single Source (Edge Case 3 — Unverified)
If a trend appears from only 1 source:
- Tag as `[UNVERIFIED]`
- Include source link (mandatory)
- Add disclaimer: "⚠️ Single source only. Verify before publishing."
- Still generate the draft — let user decide

### Check C: Sensitive Topic (Edge Case 4)
Built-in sensitive keyword list:
- Names of politicians / political parties
- War / conflict keywords (war, attack, invasion)
- Strong negative keywords (arrested, died, scam, fraud, bankrupt)

If a trend matches the sensitive list:
- Flag as `[SENSITIVE]`
- **Do NOT generate content yet**
- Ask user for confirmation: "🔴 Sensitive Trend Detected: '[name]'. Proceed? (Yes / No)"
- If Yes → generate with `[USE WITH CAUTION]` tag
- If No → skip trend, move to next

Also check against user's `blacklist` — any match = auto-discard silently.

---

## Step 5: Content Generation

For each qualified trend, generate **two parts**:

### Part 1: Content Drafts (3 Angles)

Generate 3 distinct draft options, each with Hook + Body + CTA:

1. **The "News" Angle** — Informative, insight-driven. Lead with facts and data.
2. **The "Relatable" Angle** — Empathy, humor, meme energy. Emotional hook.
3. **The "Contrarian" Angle** — Counter-intuitive take. Challenge the consensus.

All drafts must:
- Match the `brand_voice` from the spec
- Follow the `style_guide` rules if provided (e.g., "No robotic intros", "Be punchy", "Use line breaks")
- Reference real facts from the scanned sources (zero hallucination)
- Include a clear CTA that invites engagement
- Be concise and platform-ready (Twitter-length preferred)
- Never sound like a corporate press release or financial advisor

### Part 2: Reply Strategy

For each trend, identify 2–3 high-value reply targets:

- **Target 1**: Reply to the original source / thread
  - Strategy: "Value Add" — inject a deeper insight
- **Target 2**: Reply to a KOL or high-engagement commentator
  - Strategy: "Counter-Argument" or "Agreement + Expand"
- **Target 3** (optional): Reply to a skeptic / critic
  - Strategy: "Polite Pushback" — position brand expertise

Each reply target includes:
- Who to reply to (with link if available)
- Strategy label
- Draft reply text
- Soft Plug (subtle brand mention using `value_proposition`) — skip if value_proposition is missing

---

## Step 6: Output Format

Render the final output as a **React artifact** with the following structure. Read
`references/output_template.md` for the exact React component template to use.

The artifact should include:
- Header: Daily Trend Brief with date, session focus, and status
- For each trend: score breakdown, source link, summary
- Content Drafts: 3 tabs (one per angle) with Hook/Body/CTA
- Reply Strategy: collapsible cards for each target
- Tags: [TREND], [EVERGREEN], [UNVERIFIED], [SENSITIVE], [USE WITH CAUTION] as applicable
- Copy buttons for each draft and reply

---

## Brand Voice Examples

When `brand_voice` is "Degen / Witty / Sarcastic" (like CryptoAI Hub), drafts should sound like this:

**Good** (on-brand):
```
Hook: "Retail is selling ETH to pay for weddings. Meanwhile Vitalik is building a kingdom for AI." 🤖🏠
Body: 13,000 AI agents just migrated on-chain yesterday. They don't sleep. They don't take bathroom breaks. And most importantly — they don't panic sell on FUD.
CTA: "Are your bags loaded with AI gems yet, or are you still fully sidelined in stables? 👇"
```

**Bad** (off-brand):
```
Hook: "Exciting developments in the Ethereum ecosystem regarding AI agents."
Body: According to recent reports, Ethereum has seen significant growth in AI agent deployments, with over 13,000 new registrations.
CTA: "What are your thoughts on this development? Share below."
```

The difference: Good copy uses CT slang, humor, line breaks for punchiness, and speaks like a friend — not a press release. Always check the `style_guide` field for brand-specific writing rules (e.g., "No robotic intros", "Don't sound like a financial advisor").

When `brand_voice` is "Professional & Informative", tone down the slang and humor — lead with data, use clean prose, and cite sources explicitly.

---

## Success Criteria

| # | Criterion | Target |
|---|-----------|--------|
| 1 | Zero Hallucination | 100% grounded in real, verifiable sources |
| 2 | Ready-to-Post | Minimal human editing needed |
| 3 | Speed | Complete pipeline as fast as possible |

---

## Quick Reference: Decision Flow

```
START
  │
  ├─ Brand Context complete? ──No──▶ Apply Smart Defaults + Warn
  │       │ Yes
  ▼
  Ask Session Goal → (A) News / (B) Engagement / (C) Evergreen
  │
  ├─ (C) Evergreen? ──Yes──▶ Skip scan, generate from niche_keywords
  │       │ No
  ▼
  SCAN (web + Twitter/X + Reddit)
  │
  SCORE each trend (10-pt rubric)
  │
  ├─ Any trend ≥ 7? ──No──▶ Evergreen Mode [Case 1]
  │       │ Yes
  ▼
  FOR EACH qualified trend:
  │
  ├─ In blacklist? ──Yes──▶ Discard silently
  ├─ Source count == 1? ──Yes──▶ Tag [UNVERIFIED]
  ├─ Sensitive keyword? ──Yes──▶ Confirm with user [Case 4]
  │
  ▼
  GENERATE → Content Drafts + Reply Strategy
  │
  ▼
  RENDER as React artifact
```

# 📋 CHANGELOG — Social Content Sniper SKILL

## v3.0 ← v2.0

> **Summary:** v3 is a significant upgrade focused on **search engine flexibility**, **scan quality** (deduplication, source priority, freshness enforcement), **smarter reply strategy** (thread context fetching), **output template externalization**, and a new **Limitations & Boundaries** section for transparency.

---

### 🆕 New Features

#### 1. Tavily Search Engine Support (Auto-Detect)
**Section:** Step 2 — `🔍 Search Engine: Auto-Detect`

v2 relied exclusively on `web_search` + `web_fetch`. v3 introduces **Tavily** as the preferred search engine, with automatic detection and graceful fallback.

```
# v3 — Auto-detect logic (NEW)
IF TAVILY_API_KEY exists in environment:
  → engine = "tavily"
  → search = node {skillDir}/scripts/search.mjs
  → extract = node {skillDir}/scripts/extract.mjs
ELSE:
  → engine = "web_search" (fallback)
```

- Tavily commands are documented per source (Twitter, Reddit, News) with `--days`, `--topic`, `--deep` flags
- Fallback users see a one-time tip: *"💡 Set up Tavily API key for AI-optimized, deeper results"*
- Engine type is displayed in the output artifact header
- `description` frontmatter updated to mention Tavily support

#### 2. Freshness Enforcement (Mandatory Time Filters)
**Section:** Step 2 — `⏳ Freshness Enforcement (CRITICAL)`

v2 had no explicit freshness rules. v3 makes time filters **mandatory on every query** — bare queries are forbidden.

| Engine | Required Parameter |
|--------|-------------------|
| Tavily | `--days 1` (or `--days 7` for Evergreen) |
| Fallback | Append `" today"` or `" 2026"` to every query |

#### 3. Source Priority Reordering
**Section:** Step 2 — `🐦 Source Priority: Twitter → Reddit → News`

| Version | Scan Order |
|---------|-----------|
| v2 | Web News → Twitter/X → Reddit |
| v3 | **Twitter/X → Reddit → Web News** |

v3 explicitly states Twitter breaks news 30-60 minutes faster than traditional media, so it scans social-first.

#### 4. Deduplication Step (Post-Scan, Pre-Security)
**Section:** Step 2 — `🧹 Deduplication`

Entirely new step in v3. Before security checks, results are grouped by underlying event:

```
1. Group results about the same topic
2. Keep the NEWEST article as primary source
3. source_count = total articles in group
4. Merge engagement signals across sources
5. Output: deduplicated list → Security Check → Scoring
```

v2 had no dedup — the same BTC crash could consume all content slots from different outlets.

#### 5. Thread Context Fetching (Reply Strategy)
**Section:** Step 5 — `🔍 Thread Context Fetching (Before Writing Any Reply)`

The biggest content quality upgrade. v2 wrote replies based on trend summaries only. v3 **fetches and analyzes the full thread** before drafting any reply:

```
FOR EACH reply target:
  1. Fetch full thread content (Tavily extract or web_fetch)
  2. Analyze: OP message, top replies, dominating opinions, insight gaps, thread tone
  3. Write reply that fills an insight gap, not repeating existing comments
```

New additions to each reply target output:
- **Thread context summary** (2-3 sentences explaining what's dominating + the gap identified)
- Fallback tag `[NO THREAD CONTEXT]` when thread URL can't be fetched

#### 6. Limitations & Boundaries Section
**Section:** New `## Limitations & Boundaries`

Entirely new section in v3 covering four known constraints with mitigations:

| # | Limitation |
|---|-----------|
| 1 | **Data Latency** — 15-60 min delay without dedicated APIs |
| 2 | **Heuristic Scoring** — Rule-based, doesn't learn preferences |
| 3 | **Verification Scope** — Cannot verify source truthfulness |
| 4 | **Text-Only Execution** — No image/video generation or auto-posting |

---

### 🔄 Changed Behavior

#### 7. Source-Specific Search Commands (Tavily + Fallback)
**Section:** Step 2 — Sources 1, 2, 3

v2 had generic instructions ("use `web_search` with `site:x.com`"). v3 provides **exact commands per engine per source**:

| Source | v2 | v3 (Tavily) | v3 (Fallback) |
|--------|-----|-------------|----------------|
| Twitter | `web_search` with `site:x.com` | `search.mjs "{keyword} site:x.com" --topic news --days 1` | `web_search` with `"{keyword} site:x.com today"` |
| Reddit | `web_search` with `site:reddit.com` | `search.mjs "{keyword} site:reddit.com" --days 1 -n 10` | `web_search` with `"{keyword} site:reddit.com today"` |
| News | `web_search` with `"news today"` | `search.mjs "{keyword}" --topic news --days 1 -n 10` | `web_search` with `"{keyword} news today"` |

Deep search mode added for Tavily: `search.mjs "{keyword}" --deep --topic news --days 1`

#### 8. Output Template Externalized
**Section:** Step 6 — Output Format

| Version | Template Location |
|---------|------------------|
| v2 | `references/output_template.md` (relative path) |
| v3 | `/mnt/skills/user/social-content-sniper/references/output_template.md` (absolute path) |

v3 adds explicit instruction: *"Always read this file at render time — do NOT hardcode the template from memory."*

v3 also adds **search engine display** in the artifact header:
- `web_search` → shows upgrade tip to Tavily
- `tavily` → shows `Engine: Tavily (AI-optimized)`

#### 9. Brand Voice Examples Externalized
**Section:** Brand Voice Examples

| Version | Approach |
|---------|----------|
| v2 | Full examples inline (Good Degen, Good Degen Plays, Bad example — ~25 lines) |
| v3 | Externalized to `/mnt/skills/user/social-content-sniper/references/brand_voice_examples.md` |

v3 keeps only a one-line summary: *"Good copy uses CT slang, humor, line breaks — not press releases."*

#### 10. Decision Flow Updated
**Section:** Quick Reference — Decision Flow

v3 adds two new steps to the flow diagram:

```diff
  SCAN (Twitter/X → Reddit → News) [with freshness enforced]
  │
+ 🧹 DEDUPLICATE (group same-topic results)
  │
  🔒 SECURITY CHECK
```

v2's scan order was listed as `(web + Twitter/X + Reddit)` — v3 changes to `(Twitter/X → Reddit → News)` reflecting the new source priority.

---

### 🚫 Removed

#### 11. Inline Brand Voice Examples
The full inline examples (Degen Tech, Degen Plays, Bad example) were **removed from the SKILL file** and moved to an external reference file. This reduces SKILL file size and allows examples to be updated independently.

---

### 📊 Summary Matrix

| # | Change | Type | Impact |
|---|--------|------|--------|
| 1 | Tavily auto-detect + fallback | 🆕 New | Higher quality search results |
| 2 | Freshness enforcement | 🆕 New | Eliminates stale results |
| 3 | Source priority reorder | 🔄 Changed | Faster trend discovery |
| 4 | Deduplication step | 🆕 New | Prevents duplicate trends |
| 5 | Thread context fetching | 🆕 New | Smarter, non-repetitive replies |
| 6 | Limitations & Boundaries | 🆕 New | User transparency |
| 7 | Source-specific commands | 🔄 Changed | Clearer execution instructions |
| 8 | Output template externalized | 🔄 Changed | Maintainability |
| 9 | Brand voice examples externalized | 🔄 Changed | Maintainability |
| 10 | Decision flow updated | 🔄 Changed | Reflects new pipeline steps |
| 11 | Inline examples removed | 🚫 Removed | Moved to external file |

---

### ⚠️ Unchanged

The following sections are **identical** between v2 and v3 (no changes detected):

- 🔒 Security & Safety Rules (Anti-Injection, File Access Boundaries, Hard Filters)
- Step 0: Read Brand Context (fields, missing field handling, smart defaults)
- Step 1: Ask Session Goal (A/B/C options)
- Step 2: Keyword Strategy (categorized groups, session-aware priority)
- Step 3: Relevance Scoring (10-point rubric, all three dimensions, thresholds)
- Step 4: Edge Case Checks (Cases 1-4)
- Step 5: Content Drafts — Part 1 (3 angles, draft requirements)
- Step 5: Reply Strategy — base structure (3 targets, strategy labels)
- Success Criteria (Zero Hallucination, Ready-to-Post, Speed)

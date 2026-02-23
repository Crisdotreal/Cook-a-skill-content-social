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

## 2. Solution Overview

**Trend-Sniper** is an AI Skill that operates as a 24/7 market research specialist, running in three sequential steps:

```
[ SCAN ] ──▶ [ FILTER ] ──▶ [ GENERATE ]
```

1. **Scan:** Crawl multiple news sources and social media signals simultaneously.
2. **Filter:** Apply an intelligent scoring system to surface only the trends that align with the brand.
3. **Generate:** Produce a complete content strategy, including ready-to-post drafts and a Reply Strategy for community engagement.

---

## 3. Input Requirements

### 3.1 Brand Context File

Users are required to provide a configuration file `brand_context.md` containing the following fields:

| Field | Description | Usage |
|-------|-------------|-------|
| `brand_voice` | Defines the persona (e.g., Expert, Witty, Professional). | Ensures content alignment. |
| `niche_keywords` | Core topics (e.g., "AI Agents", "Layer 2", "DeFi"). | Seed for search queries. |
| `target_audience` | Detailed persona of the ideal reader. | Determines content angles. |
| `value_proposition` | The core problem the brand solves. | Used for bridging in replies. |
| `blacklist` | Topics to strictly avoid (e.g., Politics). | Safety filtering. |

### 3.2 Session Goal — Clarifying Question

Before execution, the AI will ask the user to define the session's objective:

> **"What is your focus for today?"**
>
> - **(A) News Hunting** — Breaking news & updates.
> - **(B) Community Engagement** — High-value replies & discussions.
> - **(C) Sentiment Analysis** — Market psychology (Fear/Greed).

---

## 4. Processing Logic

### Step 1: Multi-Source Scanning

Uses the search tool to retrieve real-time data:

```python
def scan_trends(keywords):
    sources = [
        "Google News (Last 24h)",
        "Social Media Discussions (High Engagement)",
        "Niche Forums (Reddit/Specialized Sites)"
    ]
    return fetch_data(sources, keywords)
```

### Step 2: Relevance Scoring System

Each discovered trend is scored on a scale of 10. **Only trends scoring above 7 are selected.**

| Dimension | Weight | Criteria |
|-----------|--------|----------|
| **Hotness** | 30% | Recency & Volume |
| **Brand Fit** | 40% | Semantic match with `brand_context.md` |
| **Viral Potential** | 30% | Controversy or Meme-ability |

### Step 3: Content Angle Development

For each selected trend, the AI develops **3 distinct content angles**:

1. **The "News" Angle** — Informativeness & Insight.
2. **The "Relatable" Angle** — Empathy & Humor (Meme).
3. **The "Contrarian" Angle** — Counter-intuitive opinion.

---

## 5. Output Deliverable

The system returns a Markdown report: **`DAILY_BRIEF_[DATE].md`**

### Part 1: Viral Content Drafts

*For owned channels (X/Twitter, LinkedIn).*

```
🚨 Trend: [Trend Name] (Score: 9/10)
Source: [Link]
Angle: Relatable/Humor

Draft Content:
"Bitcoin drops to $65k and suddenly everyone is a macro-economist? 😂

The market is just shaking out the weak hands. If you believe in the tech,
this is a discount, not a crash.

Who is buying the dip? 👇 #BTC #Crypto"
```

### Part 2: Reply Strategy

*For community engagement.*

```
🎯 Target: Reply to a trending discussion about [Topic].
Strategy: Add Value + Soft Plug.

Draft Reply:
"Valid point. The scalability issue isn't just about TPS, it's about
interoperability. We've tackled a similar challenge at [BrandName]
by focusing on..."
```

---

## 6. Technical Architecture (MVP)

| Component | Technology |
|-----------|-----------|
| **Core Engine** | OpenClaw Agent Framework |
| **LLM Model** | Claude 3.5 Sonnet / GPT-4o (Reasoning & Creative Writing) |
| **Tool: Data Retrieval** | `web_search` — For real-time data retrieval |
| **Tool: Context Ingestion** | `read_file` — For brand context ingestion |
| **Tool: Report Generation** | `write_file` — For output report generation |

---

## 7. Success Criteria

| # | Criterion | Target |
|---|-----------|--------|
| 1 | **Zero Hallucination** | 100% of content is grounded in real, verifiable facts with cited sources. |
| 2 | **Ready-to-Post** | Draft content meets 90% quality threshold, requiring minimal human editing. |
| 3 | **Speed** | The full pipeline (Scan → Draft) completes in the shortest time possible. |

---

*Document generated by AI. Last updated: 2026-02-23.*

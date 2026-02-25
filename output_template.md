# Output Template — React Artifact

Use this template as the foundation for the Daily Trend Brief artifact.
Customize content dynamically based on the pipeline results.

## Component Structure

```jsx
import { useState } from "react";

const ANGLES = ["News", "Relatable", "Contrarian"];

// Data shape for each trend
// {
//   id: 1,
//   title: "Trend Name",
//   score: { total: 9.5, hotness: 3, brandFit: 4, viralPotential: 2.5 },
//   source: "https://...",
//   summary: "Brief summary of the trend",
//   tags: ["TREND"], // or ["UNVERIFIED"], ["SENSITIVE", "USE WITH CAUTION"], ["EVERGREEN"]
//   drafts: [
//     { angle: "News", hook: "...", body: "...", cta: "..." },
//     { angle: "Relatable", hook: "...", body: "...", cta: "..." },
//     { angle: "Contrarian", hook: "...", body: "...", cta: "..." },
//   ],
//   replies: [
//     {
//       target: "Reply to @user",
//       strategy: "Value Add",
//       threadContext: "Most replies debating X vs Y. Gap: nobody mentioned Z.",
//       draft: "...",
//       link: "https://...",
//       noContext: false  // true if thread was inaccessible
//     },
//   ]
// }

export default function DailyBrief() {
  const [selectedAngle, setSelectedAngle] = useState(0);
  const [expandedReplies, setExpandedReplies] = useState({});
  const [copiedId, setCopiedId] = useState(null);

  // ========== REPLACE WITH ACTUAL DATA ==========
  const briefDate = "Feb 25, 2026";
  const sessionFocus = "Breaking News"; // or "Community Engagement" or "Evergreen Only"
  const searchEngine = "web_search"; // or "tavily"
  const warnings = []; // e.g. ["⚠️ Warning: brand_voice is missing. Applying default: Professional & Informative"]
  const trends = []; // Array of trend objects as defined above
  // ===============================================

  const statusText = trends.length > 0
    ? `✅ Found ${trends.length} Qualified Trend${trends.length > 1 ? "s" : ""}`
    : "📭 No high-scoring trends found — Evergreen mode activated";

  const copyToClipboard = (text, id) => {
    navigator.clipboard.writeText(text);
    setCopiedId(id);
    setTimeout(() => setCopiedId(null), 2000);
  };

  const toggleReply = (trendId, replyIdx) => {
    const key = `${trendId}-${replyIdx}`;
    setExpandedReplies(prev => ({ ...prev, [key]: !prev[key] }));
  };

  return (
    <div className="min-h-screen bg-gray-950 text-gray-100 p-4 md:p-8 font-mono">
      {/* Header */}
      <div className="border border-gray-700 rounded-lg p-6 mb-6 bg-gray-900">
        <h1 className="text-2xl font-bold text-amber-400 mb-2">📋 DAILY TREND BRIEF</h1>
        <div className="text-sm text-gray-400 space-y-1">
          <p>Date: {briefDate}</p>
          <p>Focus: {sessionFocus}</p>
          <p>Engine: {searchEngine === "tavily" ? "🔍 Tavily (AI-optimized)" : "🔍 web_search (basic)"}</p>
          {searchEngine !== "tavily" && (
            <p className="text-blue-400 text-xs">💡 Upgrade to Tavily for better results → tavily.com</p>
          )}
          <p className="text-green-400">{statusText}</p>
        </div>
        {warnings.map((w, i) => (
          <div key={i} className="mt-2 text-yellow-500 text-sm bg-yellow-950 border border-yellow-800 rounded p-2">{w}</div>
        ))}
      </div>

      {/* Trends */}
      {trends.map((trend) => (
        <div key={trend.id} className="border border-gray-700 rounded-lg mb-6 bg-gray-900 overflow-hidden">
          {/* Trend Header */}
          <div className="p-6 border-b border-gray-700">
            <div className="flex items-start justify-between flex-wrap gap-2">
              <h2 className="text-xl font-bold text-white">
                🚨 TREND #{trend.id}: {trend.title}
              </h2>
              <div className="flex gap-1">
                {trend.tags.map((tag, i) => (
                  <span key={i} className={`text-xs px-2 py-1 rounded font-bold ${
                    tag === "UNVERIFIED" ? "bg-yellow-900 text-yellow-300" :
                    tag === "SENSITIVE" || tag === "USE WITH CAUTION" ? "bg-red-900 text-red-300" :
                    tag === "EVERGREEN" ? "bg-blue-900 text-blue-300" :
                    "bg-green-900 text-green-300"
                  }`}>{tag}</span>
                ))}
              </div>
            </div>

            {/* Score Breakdown */}
            <div className="mt-4 bg-gray-800 rounded p-4 text-sm">
              <p className="text-lg font-bold text-amber-400 mb-2">Score: {trend.score.total}/10</p>
              <div className="grid grid-cols-3 gap-4">
                <div>
                  <p className="text-gray-400">🌡️ Hotness</p>
                  <p className="text-white font-bold">{trend.score.hotness} / 3</p>
                </div>
                <div>
                  <p className="text-gray-400">🎯 Brand Fit</p>
                  <p className="text-white font-bold">{trend.score.brandFit} / 4</p>
                </div>
                <div>
                  <p className="text-gray-400">🔥 Viral Potential</p>
                  <p className="text-white font-bold">{trend.score.viralPotential} / 3</p>
                </div>
              </div>
            </div>

            <div className="mt-3 text-sm">
              <p className="text-gray-300">{trend.summary}</p>
              <a href={trend.source} target="_blank" rel="noopener noreferrer"
                className="text-blue-400 hover:underline text-xs mt-1 inline-block">
                🔗 Source
              </a>
            </div>
          </div>

          {/* Part 1: Content Drafts */}
          <div className="p-6 border-b border-gray-700">
            <h3 className="text-lg font-bold text-amber-400 mb-4">🏗️ PART 1 — CONTENT DRAFTS</h3>

            {/* Angle Tabs */}
            <div className="flex gap-2 mb-4">
              {ANGLES.map((angle, i) => (
                <button key={i} onClick={() => setSelectedAngle(i)}
                  className={`px-4 py-2 rounded text-sm font-bold transition-colors ${
                    selectedAngle === i
                      ? "bg-amber-500 text-gray-900"
                      : "bg-gray-800 text-gray-400 hover:bg-gray-700"
                  }`}>
                  {angle}
                </button>
              ))}
            </div>

            {/* Active Draft */}
            {trend.drafts[selectedAngle] && (
              <div className="bg-gray-800 rounded-lg p-5 border border-gray-600">
                <div className="mb-3">
                  <p className="text-xs text-gray-500 uppercase tracking-wide mb-1">Hook</p>
                  <p className="text-white font-bold text-lg">{trend.drafts[selectedAngle].hook}</p>
                </div>
                <div className="mb-3">
                  <p className="text-xs text-gray-500 uppercase tracking-wide mb-1">Body</p>
                  <p className="text-gray-200 whitespace-pre-line">{trend.drafts[selectedAngle].body}</p>
                </div>
                <div className="mb-4">
                  <p className="text-xs text-gray-500 uppercase tracking-wide mb-1">CTA</p>
                  <p className="text-amber-300 font-semibold">{trend.drafts[selectedAngle].cta}</p>
                </div>
                <button
                  onClick={() => {
                    const d = trend.drafts[selectedAngle];
                    copyToClipboard(`${d.hook}\n\n${d.body}\n\n${d.cta}`, `draft-${trend.id}-${selectedAngle}`);
                  }}
                  className="bg-amber-500 hover:bg-amber-600 text-gray-900 font-bold px-4 py-2 rounded text-sm transition-colors">
                  {copiedId === `draft-${trend.id}-${selectedAngle}` ? "✅ Copied!" : "📋 Copy Draft"}
                </button>
              </div>
            )}
          </div>

          {/* Part 2: Reply Strategy */}
          <div className="p-6">
            <h3 className="text-lg font-bold text-amber-400 mb-4">💬 PART 2 — REPLY STRATEGY</h3>
            <div className="space-y-3">
              {trend.replies.map((reply, rIdx) => {
                const isExpanded = expandedReplies[`${trend.id}-${rIdx}`];
                return (
                  <div key={rIdx} className="bg-gray-800 rounded-lg border border-gray-600 overflow-hidden">
                    <button
                      onClick={() => toggleReply(trend.id, rIdx)}
                      className="w-full p-4 text-left flex justify-between items-center hover:bg-gray-750">
                      <div>
                        <span className="text-white font-bold">🎯 TARGET {rIdx + 1}:</span>
                        <span className="text-gray-300 ml-2">{reply.target}</span>
                        <span className="text-xs ml-2 px-2 py-0.5 rounded bg-gray-700 text-gray-400">
                          {reply.strategy}
                        </span>
                      </div>
                      <span className="text-gray-500">{isExpanded ? "▲" : "▼"}</span>
                    </button>
                    {isExpanded && (
                      <div className="px-4 pb-4 border-t border-gray-700 pt-3">
                        {reply.noContext && (
                          <div className="text-xs text-yellow-400 bg-yellow-950 border border-yellow-800 rounded px-2 py-1 mb-2 inline-block">
                            ⚠️ NO THREAD CONTEXT — reply based on trend summary only
                          </div>
                        )}
                        {reply.threadContext && !reply.noContext && (
                          <div className="mb-3 p-3 bg-gray-900 rounded border border-gray-600">
                            <p className="text-xs text-gray-500 uppercase tracking-wide mb-1">🔍 Why this angle?</p>
                            <p className="text-gray-400 text-sm">{reply.threadContext}</p>
                          </div>
                        )}
                        <p className="text-gray-200 whitespace-pre-line mb-3">{reply.draft}</p>
                        {reply.link && (
                          <a href={reply.link} target="_blank" rel="noopener noreferrer"
                            className="text-blue-400 hover:underline text-xs mr-4">
                            🔗 Original Thread
                          </a>
                        )}
                        <button
                          onClick={() => copyToClipboard(reply.draft, `reply-${trend.id}-${rIdx}`)}
                          className="bg-gray-700 hover:bg-gray-600 text-gray-200 font-bold px-3 py-1.5 rounded text-xs transition-colors">
                          {copiedId === `reply-${trend.id}-${rIdx}` ? "✅ Copied!" : "📋 Copy Reply"}
                        </button>
                      </div>
                    )}
                  </div>
                );
              })}
            </div>
          </div>
        </div>
      ))}
    </div>
  );
}
```

## Customization Notes

When generating the artifact, replace the placeholder data section with actual trend data from the pipeline.

The key areas to populate:
1. `briefDate` — current date
2. `sessionFocus` — from user's session goal choice
3. `searchEngine` — `"tavily"` if TAVILY_API_KEY detected, otherwise `"web_search"`
4. `warnings` — any missing field warnings from Edge Case 2
5. `trends` — array of scored & qualified trends with drafts and replies

For **Evergreen mode**, use a single trend object with `tags: ["EVERGREEN"]` and appropriate content.

## Design Tokens

The template uses a dark terminal/hacker aesthetic:
- Background: `gray-950` / `gray-900`
- Accent: `amber-400` / `amber-500`
- Tags: color-coded by severity (green=trend, yellow=unverified, red=sensitive, blue=evergreen)
- Font: `font-mono` for the terminal feel
- All interactive elements have hover states

## Copy Button Behavior

Each draft and reply has a copy button that:
1. Copies the full text to clipboard
2. Shows "✅ Copied!" feedback for 2 seconds
3. Resets to the default state

This makes it easy for Content Teams to grab drafts and paste directly into their publishing tools.

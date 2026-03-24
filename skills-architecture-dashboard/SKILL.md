---
name: skills-architecture-dashboard
description: Builds an interactive HTML dashboard visualizing the Agent Skills ecosystem by fetching live data from github.com/agentskills/agentskills. Use this skill whenever the user wants to explore, research, or visualize Skills architecture, the SKILL.md spec, ecosystem adoption, repo stats, or any Skills intelligence dashboard. Trigger when the user mentions "skills dashboard", "skills ecosystem", "skills architecture overview", "agentskills repo", or wants to analyze how different tools implement Agent Skills. Optimized for Perplexity Computer — Computer fetches all data live, no static files required.
compatibility: Optimized for Perplexity Computer Max. Computer fetches all data live via browser and GitHub API during execution. The output dashboard.html uses the GitHub API directly in the browser for Refresh. No local scripts or data files needed.
metadata:
  author: dan-avila
  version: "3.0"
  source: https://github.com/agentskills/agentskills
  optimized-for: Perplexity Computer Max
---
# Skills Architecture Dashboard
Computer fetches all data live, builds a self-contained `dashboard.html`, and embeds a **Refresh button** that re-fetches from GitHub API directly in the browser — no re-running Computer required.
---
## Model Routing (Perplexity Computer)
| Step | Assign to | Why |
|------|-----------|-----|
| Live data fetching (GitHub API + web) | **Gemini sub-agent** | Parallel fetching, Premium Sources, deep research |
| Data validation + HTML structure decisions | **Claude Opus 4.6** | Core reasoning, schema enforcement |
| Dashboard HTML + embedded JS generation | **GPT-5.3-Codex sub-agent** | Code generation, inline JS fetch logic |
| Visual design reference (optional) | **Nano Banana** | Mockup image before coding |
| Quick stat lookups | **Grok** | Speed — lightweight API calls |
> Outside Perplexity Computer: single model handles all steps.
---
## Step 0 — Optional: Design Mockup (Nano Banana)
Generate a visual reference before writing code.
```
Dark-theme developer dashboard UI. Tabs: Overview, Spec, Ecosystem, Skills, Issues.
Background #0d1117. Purple accent #7c3aed. Green accent #10b981.
Top row: stat cards (stars, forks, open issues). Below: sortable data table.
Monospace font. GitHub dark palette. Minimal, no gradients.
```
Use as reference during HTML generation. Skip if Nano Banana unavailable.
---
## Step 1 — Live Data Fetch (Gemini sub-agent)
Fetch all sources in parallel via browser and HTTP. Do NOT write to disk — hold results in memory and pass directly to the next step.
### GitHub API endpoints
```
GET https://api.github.com/repos/agentskills/agentskills
GET https://api.github.com/repos/agentskills/agentskills/contributors?per_page=10
GET https://api.github.com/repos/agentskills/agentskills/issues?state=open&per_page=5
GET https://api.github.com/repos/anthropics/skills/contents/skills
GET https://api.github.com/repos/vercel-labs/agent-skills/contents
GET https://api.github.com/repos/elastic/agent-skills/contents
GET https://api.github.com/search/repositories?q=topic:agent-skills&sort=stars&per_page=20
```
### Web sources (browse directly)
| URL | Extract |
|-----|---------|
| `https://agentskills.io/specification` | Frontmatter fields, progressive disclosure levels, optional dirs |
| `https://agentskills.io/what-are-skills` | Definition paragraph, use cases |
| `https://github.com/topics/agent-skills` | Top repos with stars and descriptions |
Pass all collected data in memory to Claude Opus 4.6 for validation.
---
## Step 2 — Validate & Structure (Claude Opus 4.6)
Receive in-memory data from Gemini. Validate and organize. Do NOT write to disk.
Expected structure before passing to HTML generation:
```
repo_stats:        stars, forks, open_issues, watchers, top contributors
spec_fields:       name, description, license, compatibility, metadata, allowed-tools
disclosure_levels: Metadata (~100 tokens) → Instructions (<5000) → Resources (unlimited)
optional_dirs:     scripts/, references/, assets/
community_repos:   list of {name, url, stars, description, updated_at}
official_skills:   {anthropic: [], vercel: [], elastic: []}
open_issues:       list of {number, title, labels, days_open, url}
```
Pass structured data to GPT-5.3-Codex for HTML generation.
---
## Step 3 — Generate Dashboard HTML (GPT-5.3-Codex sub-agent)
Build a single `dashboard.html`. All CSS and JS must be inline. No external dependencies.
### Critical: Refresh mechanism
The dashboard must include a **Refresh button** that re-fetches live data from GitHub API directly in the browser using `fetch()` — without re-running Computer.
Embed this JS logic in the HTML:
```javascript
const GITHUB_API = 'https://api.github.com';
const ENDPOINTS = {
  repo:         `${GITHUB_API}/repos/agentskills/agentskills`,
  contributors: `${GITHUB_API}/repos/agentskills/agentskills/contributors?per_page=10`,
  issues:       `${GITHUB_API}/repos/agentskills/agentskills/issues?state=open&per_page=5`,
  anthropic:    `${GITHUB_API}/repos/anthropics/skills/contents/skills`,
  community:    `${GITHUB_API}/search/repositories?q=topic:agent-skills&sort=stars&per_page=20`,
  vercel:       `${GITHUB_API}/repos/vercel-labs/agent-skills/contents`,
  elastic:      `${GITHUB_API}/repos/elastic/agent-skills/contents`,
};
async function refreshDashboard() {
  // Show loading state on all stat cards
  // Fetch all endpoints in parallel with Promise.all()
  // Update DOM elements in place — no page reload
  // Update footer timestamp
  // Handle rate limit errors gracefully (show warning, keep existing data)
}
```
The Refresh button calls `refreshDashboard()`. Initial data is already embedded from Step 1 — the button only updates when clicked.
### Dashboard tabs
**1. Overview**
- Stat cards: Stars / Forks / Open Issues / Contributors — each updates on Refresh
- "What are Skills?" summary paragraph
- Top contributors table
**2. Spec Architecture**
- SKILL.md anatomy: frontmatter block (expandable), body section, optional dirs tree
- Progressive Disclosure pyramid: Metadata → Instructions → Resources with token budgets
- Frontmatter fields table: field / required / max / description
**3. Ecosystem Map**
- Sortable table: Repo / Stars / Description / Last Updated
- Filter buttons: All / Anthropic / Vercel / Elastic / Community
- Stars and dates update on Refresh
**4. Official Skills Catalog**
- Card grid from `anthropics/skills`
- Toggle: Anthropic / Vercel / Elastic
- Each card: skill name + GitHub link
**5. Open Issues**
- Latest 5 issues: number / title / labels / days open / link
- Days open recalculates on Refresh from current timestamp
### Design requirements
- Background: `#0d1117`
- Primary accent: `#7c3aed` (purple)
- Secondary accent: `#10b981` (green)
- Monospace font: `JetBrains Mono`, fallback `Consolas`
- Responsive at 1280px+
- Vanilla JS only
- Refresh button: prominent, top-right of header, shows spinner during fetch
- Footer: `Last updated: [ISO timestamp]` — updates after each Refresh
---
## Step 4 — Deliver
1. Open `dashboard.html` in the Comet browser (Perplexity Computer)
2. Verify Refresh button works — click it once to confirm live fetch completes
3. If `present_files` available: call it with the HTML path
---
## Optional: Model Council Mode
Trigger: "Run Model Council on the Skills ecosystem"
```
Use Model Council: GPT-5.4 + Claude Opus 4.6 + Gemini 3.1 Pro in parallel.
Orchestrator: Claude Opus 4.6.
Question for all three models:
"Based on github.com/agentskills/agentskills today, what are the 3 most significant
architectural patterns emerging in Skills design, and what gaps remain unsolved?"
Synthesize: agreements, divergences, unique contributions per model.
Output: model-council-report.md
```
---
## Edge Cases
- **GitHub API rate limit**: Show a banner in the dashboard — "Rate limited. Data shown is from [timestamp]. Try again in 60 minutes." Keep existing data visible, do not clear the UI.
- **Fetch error on specific endpoint**: Render that section with "Unavailable" badge. Other sections continue to show data.
- **Nano Banana unavailable**: Skip Step 0, use design spec directly.
- **CORS on Refresh**: GitHub API allows browser requests without auth for public endpoints. If blocked, show "Open in new tab" link to the raw API URL.
---
## Output
`dashboard.html` — self-contained, opens directly in any browser, live Refresh via embedded JS.
No `data.json`. No intermediate files. Computer fetches live, HTML refreshes live.

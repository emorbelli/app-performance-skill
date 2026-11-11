**English** | [Português](README.md)

# app-performance — a Claude Skill

A skill that audits and fixes the performance of apps built with Claude (artifacts, dashboards, React/Next.js + Supabase apps, AI integrations).

Based on the 31 optimizations presented in a reel by [@gustagoat.ai](https://www.instagram.com/reel/Dfby7sPgMPU/) (Nov 2026), **filtered by what actually applies to the kind of things we build**:

| Verdict | Count | Items |
|---|---|---|
| ✅ Always apply | 18 | skeleton loading, WebP/AVIF images, deduplicated API calls, debounce, pagination, N+1 queries, remove unused deps, timeouts, AI response streaming, optimistic updates, lazy loading, parallel requests, indexes, deferred third-party scripts, list virtualization, select only needed columns, test on mobile/slow network, lightweight slow-request tracing |
| ⚠️ Conditional | 8 | queues, server close to the database, SSR/SSG, code splitting, connection pooling, unnecessary re-renders, fonts, real-user monitoring |
| 🏗️ Handled by the platform | 3 | CDN, Brotli/gzip, browser caching of static assets |
| 🚫 Don't apply by default | 2 | Redis, multiple instances / load balancing |

Details and reasoning (in Portuguese) in [`app-performance/references/checklist.md`](app-performance/references/checklist.md).

> The skill instructions are written in Portuguese. Claude follows them regardless of the language you chat in.

## Installation

**Claude Code** (personal, all projects):

```bash
git clone https://github.com/emorbelli/app-performance-skill.git
cp -r app-performance-skill/app-performance ~/.claude/skills/
```

Or per project: copy the `app-performance/` folder into the repository's `.claude/skills/`.

**Claude.ai / Claude Desktop:** zip the `app-performance/` folder and upload it under *Settings → Capabilities → Skills*.

## Usage

Just ask naturally:

- "make this app faster"
- "run a performance audit before we publish"
- "search is lagging / the table is slow"

The skill classifies the project (artifact, frontend + Supabase, or custom backend), audits only the items that apply, fixes them in order of impact, and validates on throttled network/CPU.

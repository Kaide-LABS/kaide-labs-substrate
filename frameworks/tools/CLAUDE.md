---
type: navigation
domain: operational_tooling
last_updated: 2026-05-21
key_tags:
  - operational_tooling
  - bookmarklets
  - linkedin_intel_capture_toolkit
  - step_0_tooling
  - cli_scripts
  - browser_extensions
  - mcp_configs
---

# Operational Tooling — Substrate Navigation

Browser bookmarklets, CLI scripts, MCP server configurations, and other small operational tools used during Kaide Labs sprints. Each tool documents installation, usage, downstream pipeline integration, and stability/failure modes.

These are NOT generic developer tools (those live wherever your dotfiles live). These are **Kaide-Labs-specific** tools that exist because of specific pipeline steps or substrate workflows. When the substrate accumulates a new tool, file it here.

## Tools filed

### LinkedIn Intel Capture Toolkit (4 bookmarklets, sequential workflow)

Four bookmarklets that compose into a Step 0 manual intel capture workflow. Each has a single responsibility. **Order matters** — run sequentially:

| Order | Bookmarklet | Single responsibility | File |
|---|---|---|---|
| 1 | **Expand** | Auto-click all "see more" / "show more comments" / "load more replies" buttons to expose collapsed content | `linkedin_expand_bookmarklet.md` |
| 2 | **LI Timestamps** | Decode each post's URN-encoded creation time and inject the absolute UTC datetime next to the relative-time label ("3w ago" → "3w ago · 2025-08-15 14:23 UTC") | `linkedin_timestamps_bookmarklet.md` |
| 3 | **URL inject** | Inject the canonical post URL as visible text above each post container | `linkedin_url_inject_bookmarklet.md` |
| 4 | **Clean print** | Strip LinkedIn UI chrome from the DOM and replace the page body with cleaned plaintext (also auto-copies to clipboard) | `linkedin_clean_print_bookmarklet.md` |

### Why four tools, not one

Each tool has a single, distinct DOM operation. Combining them would (a) make debugging harder when LinkedIn changes their DOM, (b) couple unrelated concerns (button-clicking, URN decoding, attribute extraction, DOM stripping), and (c) prevent partial use (e.g., capturing pure post text without URL/timestamp injection for cases where only verbatim content is needed). Four tools, four responsibilities, four failure surfaces — easier to maintain, easier to extend.

### Why this specific order

- **Expand must run first** — the other three tools operate on the DOM as it currently exists. Collapsed content (truncated long posts, hidden comment threads, hidden reply chains) is literally not in the DOM until the relevant button is clicked. Running URL inject or Clean print before Expand means missing the highest-signal content.
- **LI Timestamps before URL inject** — Timestamps modifies the existing time-label area (injects next to "3w ago"); URL inject prepends a new block above the post. Running URL inject first would push the time label further from the rendered URL block, making them visually disconnected in the captured plaintext.
- **Clean print must run last** — Clean print destroys the original DOM by replacing the page body with cleaned plaintext. Any bookmarklet that operates on the original DOM must run before this one.

## The full capture workflow

1. **Navigate** to the prospect's LinkedIn profile → Activity tab → "All posts" (or feed / content tab / specific post URL — any page rendering LinkedIn's standard post containers)
2. **Scroll** down to load the posts you want to capture (LinkedIn lazy-loads; posts off-screen aren't in the DOM yet)
3. **Click Expand bookmarklet** — a green banner appears showing live click count. Buttons get auto-clicked at a 1.5s interval as expandable content is detected.
4. **Wait** until the click counter stops incrementing (~10–20 seconds for an active profile; longer for posts with deep comment threads)
5. **Click Expand bookmarklet again** to STOP the interval. Banner turns gray ("LinkedIn Expander Stopped").
6. **Click LI Timestamps bookmarklet** — a blue banner appears showing how many posts got stamped. Each post's relative-time label ("3w") now also shows the absolute UTC datetime ("3w · 2025-08-15 14:23 UTC"). Banner auto-dismisses after 5 seconds. **Live mode is active** — newly-loaded posts (if you scroll more) get stamped automatically via MutationObserver.
7. **Click URL inject bookmarklet** — one-shot operation. Each post container now has a blue-bordered URL bar above it showing the canonical post URL. Console (F12) shows confirmation: `Kaide URL injector: tagged N posts`.
8. **Click Clean print bookmarklet** — page strips images/buttons/SVGs/iframes; the body is replaced with cleaned plaintext rendering. Clipboard is auto-populated with the cleaned text (silently).
9. **Paste** (Ctrl+V) into `[prospect]_intel.md`. URLs and timestamps come along with the post content as visible text. Step 01 (intel cleaner) preserves them as load-bearing content downstream.

## Downstream pipeline integration

- **Step 0** (manual intel gathering) is where this toolkit runs. See `pipeline/pipeline_overview.md`.
- **Step 01** (intel cleaner) at `pipeline/templates/STEP_01_INTEL_CLEANER_TEMPLATE.md` preserves the injected URLs and timestamps as load-bearing content rather than UI chrome.
- **Step 02** (founder dossier expansion) at `pipeline/templates/STEP_02_DOSSIER_TEMPLATE.md` cites the URLs and timestamps directly as primary sources during dossier generation. A dossier claim like *"In a LinkedIn post dated 2025-08-15, [Founder] stated..."* requires both the URL (for verification) and the timestamp (for context — what was the founder's stage when they made this claim?).

The toolkit makes downstream citation rigor possible. Without it, downstream pipeline steps lose the ability to anchor claims to specific posts with specific dates — degrading dossier quality and substrate honesty.

## When to add a new tool

A tool earns a place here when:

1. It's used in at least one pipeline step
2. It would be non-obvious to reconstruct from scratch (or would cost more than 10 minutes to rediscover)
3. It has stability concerns or failure modes worth documenting

Tools that don't meet these criteria (e.g., generic Python utilities, one-off shell aliases) live in your personal dotfiles, not the substrate.

## File structure for new tools

Each tool file should cover:

- The problem it solves
- The code (one-liner for the bookmarklet URL field + formatted version for reference)
- Installation
- Usage (with explicit position in any multi-tool workflow)
- What it does / does not do
- Stability and failure modes (LinkedIn DOM changes break bookmarklets silently — document detection + recovery)
- Alternative tools considered and rejected (for substrate record)
- Cross-references to related substrate content

Follow the structure of `linkedin_url_inject_bookmarklet.md` as the canonical reference.

## Cross-reference

- `pipeline/pipeline_overview.md` — where tools plug into the pipeline
- `pipeline/templates/STEP_01_INTEL_CLEANER_TEMPLATE.md` — preserves bookmarklet outputs during cleanup
- `pipeline/templates/STEP_02_DOSSIER_TEMPLATE.md` — cites bookmarklet-captured URLs and timestamps as primary sources

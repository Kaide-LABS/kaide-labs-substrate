---
type: framework
domain: operational_tooling
last_updated: 2026-05-21
canonical: true
key_tags:
  - linkedin_scraping
  - intel_capture
  - bookmarklet
  - urn_decoding
  - snowflake_id_timestamp
  - dom_injection
  - mutation_observer
  - step_0_tooling
  - browser_tool
  - linkedin_intel_capture_toolkit
  - tool_2_of_4
toolkit_position: 2
toolkit_runs_after: linkedin_expand_bookmarklet
toolkit_runs_before: linkedin_url_inject_bookmarklet
runs_in: chromium_or_firefox_browser
upstream_step: step_0_manual_intel_capture
downstream_step: linkedin_url_inject_bookmarklet
---

# **LINKEDIN TIMESTAMPS BOOKMARKLET (LI TIMESTAMPS)**

Browser bookmarklet that decodes each LinkedIn post's URN-encoded creation timestamp and injects the absolute UTC datetime as visible text next to the post's relative-time label. Installs a `MutationObserver` so newly-loaded posts get stamped automatically as the page scrolls. **Tool 2 of 4** in the LinkedIn Intel Capture Toolkit (see `CLAUDE.md` in this folder).

## **1. THE PROBLEM THIS SOLVES**

LinkedIn renders post timestamps as relative-time labels: "3w" / "2mo" / "1y ago". These are fine for casual reading but useless for substrate-grade intel. A claim like *"In a post from 2025-08-15, Founder X stated..."* is verifiable; a claim like *"In a 3w-ago post, Founder X stated..."* is unverifiable two months later when the relative time has drifted.

Step 02 (Founder Dossier Expansion) requires primary-source citations with dates. Without absolute timestamps in the captured intel, the dossier expansion either fabricates dates (substrate violation) or skips date citations (degraded dossier quality).

The bookmarklet solves this by reading each post's activity URN, decoding the snowflake-ID timestamp embedded in the high bits, formatting it as `YYYY-MM-DD HH:MM UTC`, and injecting it next to the existing relative-time label. The relative label stays for context; the absolute datetime gets captured during Ctrl+A → Ctrl+C.

### **How the URN decode works (technical)**

LinkedIn's activity URNs (e.g., `urn:li:activity:7234567890123456789`) encode the post's creation time in the high bits of the integer, in the snowflake-ID format common to social platforms. The decode is a single right-shift:

```
timestamp_ms = activity_id >> 22
```

This bookmarklet uses `BigInt` for the shift (regular JavaScript Number loses precision on 18-digit integers), then constructs a `Date` from the resulting milliseconds-since-epoch. No API call required, no network round-trip — the timestamp is encoded in the URN itself and available client-side.

## **2. POSITION IN THE TOOLKIT**

**Run SECOND** (after Expand, before URL inject).

- **After Expand** because the timestamp bookmarklet needs the full post set loaded into the DOM. Posts collapsed behind "Show previous comments" don't have URNs accessible until the comment threads are expanded.
- **Before URL inject** because Timestamps modifies the existing time-label area (injects next to "3w ago"), while URL inject prepends a new block above the post. Running URL inject first would push the time label visually further from the new URL block in the captured plaintext — separating data that belongs together for citation purposes.

Full toolkit workflow: **expand → LI Timestamps → URL inject → Clean print → paste into intel.md**.

## **3. INSTALLATION**

1. Open browser (Chrome, Edge, or Firefox)
2. Right-click the bookmarks bar → "Add bookmark" / "Add page"
3. **Name:** `Kaide LI Timestamps`
4. **URL field:** paste the full code below as a single line.
5. Save

### **The bookmarklet code (single line, paste verbatim into URL field):**

```javascript
javascript:(function(){const DECODE=id=>{try{return Number(BigInt(id)>>22n)}catch(e){return null}};const FMT=ms=>{const d=new Date(ms);const pad=n=>String(n).padStart(2,'0');return`${d.getUTCFullYear()}-${pad(d.getUTCMonth()+1)}-${pad(d.getUTCDate())} ${pad(d.getUTCHours())}:${pad(d.getUTCMinutes())} UTC`};const MARK='data-kaide-stamped';const stamp=root=>{const posts=root.querySelectorAll('[data-urn^="urn:li:activity:"]:not(['+MARK+']),[data-id^="urn:li:activity:"]:not(['+MARK+'])');let n=0;posts.forEach(p=>{const urn=p.getAttribute('data-urn')||p.getAttribute('data-id');const m=urn&&urn.match(/urn:li:activity:(\d+)/);if(!m)return;const ts=DECODE(m[1]);if(!ts)return;const label=FMT(ts);const timeNode=p.querySelector('.update-components-actor__sub-description,.feed-shared-actor__sub-description,time, span.visually-hidden + span');const tag=document.createElement('span');tag.textContent=' · '+label;tag.style.cssText='color:#0a66c2;font-weight:600;margin-left:4px;font-size:0.85em;';if(timeNode&&timeNode.parentNode){timeNode.parentNode.insertBefore(tag,timeNode.nextSibling)}else{const header=p.querySelector('.update-components-actor,.feed-shared-actor');if(header)header.appendChild(tag)}p.setAttribute(MARK,'1');n++});return n};const initial=stamp(document.body);const obs=new MutationObserver(muts=>{let added=false;muts.forEach(m=>m.addedNodes.forEach(node=>{if(node.nodeType===1)added=true}));if(added)stamp(document.body)});obs.observe(document.body,{childList:true,subtree:true});window.__kaideTimestampObs=obs;const banner=document.createElement('div');banner.textContent=`Kaide timestamps active. Stamped ${initial} loaded posts. Live mode on. Click to dismiss.`;banner.style.cssText='position:fixed;top:12px;right:12px;z-index:99999;background:#0a66c2;color:#fff;padding:10px 14px;border-radius:6px;font:13px system-ui;box-shadow:0 4px 12px rgba(0,0,0,0.2);cursor:pointer;max-width:300px;';banner.onclick=()=>banner.remove();document.body.appendChild(banner);setTimeout(()=>banner.remove(),5000);})();
```

## **4. USAGE**

1. **Prerequisite:** Expand bookmarklet has run and you've clicked it again to stop the interval (banner shows "LinkedIn Expander Stopped")
2. Click the LI Timestamps bookmarklet
3. A blue banner appears in the top-right: `Kaide timestamps active. Stamped N loaded posts. Live mode on. Click to dismiss.` The banner shows how many posts got stamped in the initial pass.
4. Each post's relative-time label now shows the absolute UTC datetime appended after it. Example: `3w · 2025-08-15 14:23 UTC`. Styled in LinkedIn-blue, bold, slightly smaller font so it visually integrates without dominating.
5. **Live mode is active automatically.** If you scroll further to load more posts, the `MutationObserver` detects the new posts in the DOM and stamps them too. No need to re-click the bookmarklet.
6. The banner auto-dismisses after 5 seconds. You can also click it to dismiss immediately.
7. Proceed to the URL inject bookmarklet (tool 3).

### **Why live mode matters**

LinkedIn's lazy-loading means posts get added to the DOM as you scroll. Without live mode, you'd need to scroll first, then click Timestamps. With live mode, you can scroll and the timestamps just appear on newly-loaded posts as they enter the DOM. This is the right ergonomics for the typical capture flow: scroll-and-stamp-and-scroll-and-stamp until you've captured the time range you care about.

The MutationObserver is left running indefinitely after the bookmarklet fires (`window.__kaideTimestampObs` holds the reference). It's lightweight — only triggers `stamp(document.body)` when nodes are added to the DOM, and the `:not([data-kaide-stamped])` selector means already-stamped posts are skipped. To stop the observer, navigate to a different page (it dies with the page).

## **5. WHAT IT INJECTS / DOES NOT INJECT**

### **What it stamps:**

The bookmarklet identifies posts via two attribute selectors:
- `[data-urn^="urn:li:activity:"]` — the standard LinkedIn post container attribute
- `[data-id^="urn:li:activity:"]` — alternative attribute LinkedIn uses on some surfaces

For each matched post, it extracts the activity ID from the URN, decodes the timestamp, formats as `YYYY-MM-DD HH:MM UTC`, and injects a styled `<span>` containing ` · {datetime}` next to the existing time label. The injection point is one of (in priority order):

1. `.update-components-actor__sub-description` — standard feed post time label
2. `.feed-shared-actor__sub-description` — legacy time label class
3. `<time>` element — semantic HTML time element if present
4. `span.visually-hidden + span` — the visible sibling of LinkedIn's accessibility-hidden full-date label
5. Fallback: appended to `.update-components-actor` or `.feed-shared-actor` (the post header container)

Each stamped post is marked with `data-kaide-stamped="1"` to prevent double-stamping.

### **What it does NOT stamp:**

- **Comments** — comments have their own URNs (`urn:li:comment:...`) but the bookmarklet only matches `urn:li:activity:`. Comment URNs use a different timestamp encoding and would need separate decode logic. If comment timestamps are needed for a specific prospect, extend the matcher.
- **Sponsored content** — usually doesn't carry the standard `data-urn` attribute. Not stamped, which is desirable.
- **External article previews / video embeds** — don't have activity URNs. Not stamped.
- **The user's own posts when viewing your own profile** — same URN structure as everyone else; gets stamped normally if present.

### **Edge cases worth knowing:**

- **Timezone:** All timestamps are formatted in **UTC**, not local time. This is deliberate — substrate-grade citation needs a consistent timezone, and UTC is the canonical choice. If you need to convert to local time later, the format includes the timezone label (` UTC`).
- **Reposted posts:** The outer container has the repost activity's URN; the inner (original) post has its own activity URN. Both get stamped with their respective creation times. This is correct behavior — the repost happened at the outer time, the original was posted at the inner time.
- **Old posts (>1 year):** Decode works the same way for any activity URN since LinkedIn started using snowflake IDs (~2017). The bookmarklet doesn't care how old the post is.
- **Posts with no time label visible** — rare but possible (LinkedIn sometimes A/B tests UI). The fallback strategy (append to post header container) catches these.

## **6. STABILITY AND FAILURE MODES**

### **URN format stability**

The activity URN format (`urn:li:activity:{19-digit-integer}`) and the snowflake-ID timestamp encoding (`timestamp_ms = activity_id >> 22`) have been stable on LinkedIn for many years. This is one of the most stable parts of LinkedIn's data model — they would have to migrate billions of existing URNs to break it. Low concern.

### **DOM injection-point stability**

The injection-point selectors (`.update-components-actor__sub-description` etc.) are more brittle. LinkedIn occasionally renames these classes. The fallback chain (4 strategies + 1 last-resort append) means at least one strategy usually works even after class renames.

### **Detection of failure**

After clicking the bookmarklet, the blue banner shows `Stamped N loaded posts`. If N=0 on a page that clearly has posts visible, either:
- The page has no `data-urn` attributes (rare — would mean a different LinkedIn surface)
- All posts on the page have already been stamped from a prior bookmarklet run (the page survived a soft-reload — the marks are on the DOM, but new DOM nodes since the prior run also weren't stamped because the `MutationObserver` from the prior run was killed by the soft-reload)

Open the browser console (F12) and look for the activity URNs manually:
```javascript
document.querySelectorAll('[data-urn^="urn:li:activity:"]').length
```

If that count is >0 but the bookmarklet stamped 0, the injection logic broke (rather than the URN extraction). If the count is also 0, LinkedIn changed the post container attribute and the bookmarklet's matcher needs updating.

### **Recovery**

If the bookmarklet stops stamping, the snowflake decode is still trivially computable manually:

```javascript
// In browser console:
const urn = 'urn:li:activity:7234567890123456789'; // copy from a post element
const id = urn.match(/(\d+)/)[1];
const ts = Number(BigInt(id) >> 22n);
new Date(ts).toISOString(); // 2024-09-15T14:23:45.000Z
```

Manual fallback is workable but tedious. Update the bookmarklet's injection-point selectors when LinkedIn DOM changes; the decode logic itself rarely needs touching.

## **7. ALTERNATIVE TOOLS CONSIDERED (REJECTED)**

- **LinkedIn API for post metadata** — requires partner access; not viable.
- **Hovering each post to reveal native full-date tooltip** — works manually but doesn't get captured by Ctrl+A → Ctrl+C (the tooltip is rendered on hover, not in the static DOM).
- **Parsing relative-time strings ("3w" → calendar math)** — error-prone and timezone-ambiguous. Snowflake decode is exact.
- **A standalone Python script that takes URNs and returns timestamps** — works but requires copying URNs out of the browser, which defeats the point of capturing them inline with post content.

The bookmarklet's snowflake decode is exact, instant, requires no network round-trip, and integrates naturally into the existing capture workflow.

## **8. WHY UTC SPECIFICALLY**

Substrate timestamps need to be deterministic and globally meaningful. Local time depends on:
- The capturer's browser timezone (Lagos? UK? US?)
- DST shifts (which vary by region and date)
- Whether the post was made in the prospect's timezone vs the capturer's

UTC sidesteps all of this. When Step 02 (Founder Dossier Expansion) cites a post timestamp, the UTC datetime is unambiguous and reproducible — anyone else looking at the URN later can decode it to the same UTC datetime and verify.

If a specific prospect engagement needs the post's local time (e.g., "this was posted at 3am in their local timezone, suggesting irregular work hours"), that conversion happens at Step 02 explicitly with the prospect's known timezone, not at capture time.

## **9. CROSS-REFERENCE**

- `CLAUDE.md` (this folder) — toolkit navigation and full capture workflow
- `linkedin_expand_bookmarklet.md` — tool 1 of 4 (runs before this)
- `linkedin_url_inject_bookmarklet.md` — tool 3 of 4 (runs after this)
- `linkedin_clean_print_bookmarklet.md` — tool 4 of 4
- `pipeline/templates/STEP_02_DOSSIER_TEMPLATE.md` — downstream consumer; cites timestamps as primary-source dates
- `pipeline/templates/STEP_01_INTEL_CLEANER_TEMPLATE.md` — preserves the injected timestamps during cleanup

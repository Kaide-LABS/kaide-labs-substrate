---
type: framework
domain: operational_tooling
last_updated: 2026-05-21
canonical: true
key_tags:
  - linkedin_scraping
  - intel_capture
  - bookmarklet
  - dom_button_clicking
  - step_0_tooling
  - browser_tool
  - linkedin_intel_capture_toolkit
  - tool_1_of_4
toolkit_position: 1
toolkit_runs_before: linkedin_timestamps_bookmarklet
runs_in: chromium_or_firefox_browser
upstream_step: step_0_manual_intel_capture
downstream_step: linkedin_timestamps_bookmarklet
---

# **LINKEDIN EXPAND BOOKMARKLET**

Browser bookmarklet that auto-clicks every "see more" / "show more comments" / "load more replies" / "view more comments" button on a LinkedIn page. Runs as an interval that keeps clicking newly-detected expandable elements until manually stopped. **Tool 1 of 4** in the LinkedIn Intel Capture Toolkit (see `CLAUDE.md` in this folder).

## **1. THE PROBLEM THIS SOLVES**

LinkedIn collapses content aggressively. Long posts truncate at "...see more". Comment threads collapse to "Show previous comments" buttons. Reply chains hide behind "Load more replies". When you Ctrl+A → Ctrl+C a LinkedIn page, the collapsed content is **not in the DOM** — it never loads until the button is clicked.

For Step 0 intel capture, missing this content means missing high-signal founder posts (often the most thoughtful posts are the long-form ones that get truncated), missing comment threads (which often contain the founder's own follow-up replies), and missing engagement context.

Manually clicking every "see more" button on an active profile is 50–200 individual clicks. The expander automates this — click once to start, wait for the count to settle, click again to stop. The page is now fully expanded and ready for the rest of the toolkit.

## **2. POSITION IN THE TOOLKIT**

**Run FIRST.** The other three bookmarklets (LI Timestamps, URL inject, Clean print) operate on the DOM as it currently exists — if content is collapsed, they don't see it. Expanding first ensures the subsequent tools have the full content surface to operate on.

Full toolkit workflow: **expand → LI Timestamps → URL inject → Clean print → paste into intel.md**.

## **3. INSTALLATION**

1. Open browser (Chrome, Edge, or Firefox)
2. Right-click the bookmarks bar → "Add bookmark" / "Add page"
3. **Name:** `Kaide LinkedIn Expand`
4. **URL field:** paste the full code below as a single line. The code starts with `javascript:` and is what makes it a bookmarklet rather than a regular bookmark.
5. Save

### **The bookmarklet code (single line, paste verbatim into URL field):**

```javascript
javascript:(function(){if(window.__kaideExpander){clearInterval(window.__kaideExpander);window.__kaideExpander=null;document.getElementById('kaide-expander-banner')?.remove();const off=document.createElement('div');off.textContent='LinkedIn Expander Stopped.';off.style.cssText='position:fixed;bottom:12px;right:12px;z-index:99999;background:#666;color:#fff;padding:10px 14px;border-radius:6px;font:13px system-ui;';document.body.appendChild(off);setTimeout(()=>off.remove(),2000);return}const M='data-kaide-clicked';const SEL=['.social-details-social-counts__comments button','button[class*="show-prev-replies"]','button[class*="show-previous-replies"]','button[class*="show-more-comments"]','button[class*="load-more-comments"]','button[class*="show-prev-comments"]','button[class*="show-previous-comments"]','button[class*="show-more-replies"]','button[class*="load-more-replies"]','button[class*="see-more"]','button[class*="see-more-less-toggle"]','button.feed-shared-inline-show-more-text__see-more-less-toggle','button[aria-label*="more comments" i]','button[aria-label*="more replies" i]','button[aria-label*="previous comments" i]','button[aria-label*="previous replies" i]'];const P=[/^…?\.{0,3}more$/,/^…?see more$/,/^show more comments?$/,/^load more comments?$/,/^show previous comments?$/,/^load previous comments?$/,/^view more comments?$/,/^show more repl(y|ies)$/,/^load more repl(y|ies)$/,/^show previous repl(y|ies)$/,/^see previous repl(y|ies)$/];const NEVER=new Set(['comment','reply','repost','share','send','like']);let n=0;const upd=()=>{const b=document.getElementById('kaide-expander-banner');if(b)b.querySelector('.cnt').textContent=n};const hit=el=>{if(!el||el.hasAttribute(M))return;try{el.click();el.setAttribute(M,'1');n++;upd()}catch(e){}};const tick=()=>{SEL.forEach(s=>{try{document.querySelectorAll(`${s}:not([${M}])`).forEach(hit)}catch(e){}});document.querySelectorAll(`button:not([${M}]),span:not([${M}])`).forEach(el=>{const t=(el.innerText||el.textContent||'').replace(/\s+/g,' ').trim().toLowerCase();if(!t||NEVER.has(t))return;if(P.some(r=>r.test(t)))hit(el)})};window.__kaideExpander=setInterval(tick,1500);tick();const banner=document.createElement('div');banner.id='kaide-expander-banner';banner.innerHTML='LinkedIn Expander Active · <span class="cnt">0</span> clicks<br><small>Click bookmarklet again to stop.</small>';banner.style.cssText='position:fixed;bottom:12px;right:12px;z-index:99999;background:#057642;color:#fff;padding:10px 14px;border-radius:6px;font:13px system-ui;box-shadow:0 4px 12px rgba(0,0,0,0.2);max-width:280px;';document.body.appendChild(banner)})();
```

## **4. USAGE**

1. Navigate to the prospect's LinkedIn profile → Activity tab → "All posts" (or feed / content tab — anywhere LinkedIn renders posts with the standard expandable controls)
2. Scroll down to load all the posts you want to capture (LinkedIn lazy-loads; you must scroll to load posts into the DOM)
3. Click the bookmarklet
4. A green banner appears in the bottom-right: `LinkedIn Expander Active · 0 clicks`. The click counter increments as buttons get auto-clicked.
5. **Wait** until the click counter stops incrementing (~10–20 seconds for an active profile; can be longer for posts with deep comment threads). The interval runs every 1500ms, so newly-loaded content (e.g., a comment thread that just expanded and now shows its own "Load more replies" button) gets caught on the next tick.
6. **Click the bookmarklet again** to stop the interval. The green banner is replaced by a gray "LinkedIn Expander Stopped." toast that auto-dismisses after 2 seconds.
7. Proceed to the LI Timestamps bookmarklet (tool 2).

### **Why the toggle-to-stop pattern**

Unlike the other three bookmarklets (one-shot push operations), Expand runs continuously because LinkedIn loads expandable content lazily — a "Show previous comments" button that gets clicked reveals 5 more comments, each of which may have its own "Load more replies" button. A one-shot expand would miss the cascade. The interval keeps catching new buttons until you tell it to stop.

The toggle-to-stop matters because the interval also runs if you scroll to load more posts — newly-loaded posts get their buttons auto-clicked too. This is desirable when you're still scrolling to load content; undesirable once you're ready to proceed to the next toolkit step (because the interval would keep firing in the background, slowing the page).

## **5. WHAT IT INJECTS / DOES NOT INJECT**

### **What it clicks:**

The bookmarklet identifies expandable buttons via two strategies:

**Strategy 1 — Direct CSS selectors** (fastest, most reliable):
- `.social-details-social-counts__comments button` (the top-level "View comments" button on posts)
- Buttons with classes containing: `show-prev-replies`, `show-previous-replies`, `show-more-comments`, `load-more-comments`, `show-prev-comments`, `show-previous-comments`, `show-more-replies`, `load-more-replies`, `see-more`, `see-more-less-toggle`
- The specific class `button.feed-shared-inline-show-more-text__see-more-less-toggle` (the per-post "...see more" toggle)
- Buttons with `aria-label` containing (case-insensitive): "more comments", "more replies", "previous comments", "previous replies"

**Strategy 2 — Text-content pattern matching** (slower, catches what selectors miss):
- Matches text against regex patterns: `…more` / `…see more` / `show more comments` / `load more comments` / `show previous comments` / `load previous comments` / `view more comments` / `show more reply` / `load more reply` / `show previous reply` / `see previous reply`
- Excludes elements with text matching the NEVER set: `comment`, `reply`, `repost`, `share`, `send`, `like` (these are action buttons, not expansion buttons)

Each clicked element is marked with `data-kaide-clicked="1"` to prevent double-clicking on subsequent ticks. This makes the bookmarklet idempotent — clicking the interval tick on an already-fully-expanded page does nothing.

### **What it does NOT click:**

- Action buttons (like, comment, repost, share, send) — these are explicitly excluded via the NEVER set
- "Follow" / "Connect" / "Subscribe" buttons — no text match
- Notification toast dismiss buttons — no text match
- Page navigation buttons — no text match
- Any button it has already clicked once (the `data-kaide-clicked` mark prevents repeats)

### **Edge cases worth knowing:**

- **Reposted posts (nested URNs)** — the bookmarklet expands content on both the outer (repost) and inner (original post) containers. Both get expanded; both get URL-injected (by tool 3) on the outer URN.
- **Sponsored content** — usually rendered with different button patterns; mostly ignored. This is desirable behavior for intel capture.
- **Embedded articles / video previews** — the bookmarklet does not interact with these. They remain as embedded previews; their content is not captured.
- **"Connect" / "Send a message" modal dialogs** — never clicked, even if the modal somehow contained a matching text pattern.

## **6. STABILITY AND FAILURE MODES**

### **LinkedIn DOM stability**

LinkedIn occasionally restructures their frontend. The class names that include `see-more`, `show-more-comments`, etc. have been moderately stable but are not guaranteed. The fallback text-content pattern matching catches most class-name churn — even if LinkedIn renames `button.feed-shared-inline-show-more-text__see-more-less-toggle` to something else, the button's visible text is still `"…more"` and gets caught by the regex layer.

### **Detection of failure**

After clicking the bookmarklet, the green banner shows a click counter. If the counter stays at 0 for >5 seconds on a page where you can visually see "see more" buttons, the bookmarklet's selectors are no longer matching LinkedIn's current DOM.

### **Recovery**

If the bookmarklet stops working, manual button-clicking is the fallback (slower, reliable). Update the bookmarklet's SEL array and P regex array based on inspecting the new button DOM structure (right-click button → Inspect → note the new class names or aria-label). The bookmarklet code is designed to be easy to extend — add new selectors to SEL, add new text patterns to P.

### **Performance characteristics**

- The interval runs at 1500ms — fast enough to feel responsive, slow enough not to peg CPU
- On an idle page (all expansion complete), each tick takes <10ms; the bookmarklet is effectively free to leave running
- On an active page (many expansions firing), each tick can take 100–200ms; barely noticeable
- The MutationObserver pattern was considered and rejected — interval + idempotent marking is simpler and handles all observed LinkedIn behaviors

## **7. ALTERNATIVE TOOLS CONSIDERED (REJECTED)**

For the substrate record — these were considered and rejected as alternatives:

- **LinkedIn API direct access** — requires partner-tier API access that LinkedIn doesn't grant for our use case. Also generates audit signals on the prospect side.
- **Browser extension (Phantombuster, Apollo, etc.)** — paid services with rate limits and account-suspension risk. Overkill for low-volume intel capture.
- **Selenium / Playwright scraper** — engineering overhead, fragility, and IP-block risk. Not worth maintaining a scraper for low-volume use case.
- **Manual click-every-button** — works but takes 5–10 minutes per profile. The bookmarklet automates this to ~30 seconds of attention plus ~20 seconds of waiting for the counter to settle.

The bookmarklet is the right balance of speed, reliability, and minimal overhead.

## **8. CROSS-REFERENCE**

- `CLAUDE.md` (this folder) — toolkit navigation and full capture workflow
- `linkedin_timestamps_bookmarklet.md` — tool 2 of 4 (runs after Expand)
- `linkedin_url_inject_bookmarklet.md` — tool 3 of 4
- `linkedin_clean_print_bookmarklet.md` — tool 4 of 4
- `pipeline/templates/STEP_01_INTEL_CLEANER_TEMPLATE.md` — downstream consumer of the captured intel
- `pipeline/pipeline_overview.md` — where Step 0 (and this toolkit) sits in the pipeline

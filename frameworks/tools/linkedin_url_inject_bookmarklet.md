---
type: framework
domain: operational_tooling
last_updated: 2026-05-21
canonical: true
key_tags:
  - linkedin_scraping
  - intel_capture
  - bookmarklet
  - dom_injection
  - url_extraction
  - step_0_tooling
  - browser_tool
  - linkedin_intel_capture_toolkit
  - tool_3_of_4
toolkit_position: 3
toolkit_runs_after: linkedin_timestamps_bookmarklet
toolkit_runs_before: linkedin_clean_print_bookmarklet
runs_in: chromium_or_firefox_browser
upstream_step: step_0_manual_intel_capture
downstream_step: linkedin_clean_print_bookmarklet
---

# **LINKEDIN URL INJECTION BOOKMARKLET**

Browser bookmarklet that surfaces canonical LinkedIn post URLs inline with post content during Step 0 intel capture. Inject before Clean print; the URLs come along in the captured text and downstream cleaning steps preserve them as high-signal content rather than UI chrome. **Tool 3 of 4** in the LinkedIn Intel Capture Toolkit (see `CLAUDE.md` in this folder).

## **1. THE PROBLEM THIS SOLVES**

During Step 0 intel capture on a prospect's LinkedIn profile, manual capture pulls post text, comment text, reaction counts, and career history — but loses the per-post URLs because they're encoded in HTML attributes (`data-urn` on the post container) rather than rendered as visible text.

Without URLs, downstream pipeline steps lose the ability to cite specific posts. Step 02 (Founder Dossier Expansion) explicitly requires primary-source URLs for every claim; if intel.md has post text but no URLs, the dossier expansion has to either skip citations or fabricate them. Both outcomes degrade substrate quality.

The bookmarklet surfaces every post's URL as visible text right above the post body, so Ctrl+A → Ctrl+C (or the Clean print bookmarklet's auto-copy) captures the URL alongside the post content. Step 01 (Intel Cleaner) preserves these URLs as load-bearing content; Step 02 cites them directly in dossier expansion.

## **2. POSITION IN THE TOOLKIT**

**Run THIRD** (after Expand and LI Timestamps, before Clean print).

- **After Expand** because the URL injection needs the full post set loaded into the DOM. Collapsed content doesn't get URLs injected.
- **After LI Timestamps** because Timestamps modifies the existing time-label area (next to "3w"); URL inject prepends a new visible block above the post. Running URL inject first would push the time label visually further from the new URL block, separating data that belongs together for citation purposes.
- **Before Clean print** because Clean print destroys the original DOM. URLs must be injected as visible text before that destruction happens, so they survive into the cleaned plaintext.

Full toolkit workflow: **expand → LI Timestamps → URL inject → Clean print → paste into intel.md**.

## **3. INSTALLATION**

1. Open browser (Chrome, Edge, or Firefox)
2. Right-click the bookmarks bar → "Add bookmark" / "Add page"
3. **Name:** `Kaide LinkedIn URL Inject`
4. **URL field:** paste the full code below as a single line.
5. Save

### **The bookmarklet code (single line, paste verbatim into URL field):**

```javascript
javascript:(function(){const posts=document.querySelectorAll('[data-urn^="urn:li:activity:"]');posts.forEach(post=>{if(post.querySelector('.kaide-url-tag'))return;const urn=post.getAttribute('data-urn');const url=`https://www.linkedin.com/feed/update/${urn}/`;const tag=document.createElement('div');tag.className='kaide-url-tag';tag.style.cssText='font-family:monospace;font-size:11px;color:#0a66c2;padding:4px 8px;background:#f3f6f8;border-left:3px solid #0a66c2;margin:4px 0;word-break:break-all;';tag.textContent=url;post.insertBefore(tag,post.firstChild);});console.log(`Kaide URL injector: tagged ${posts.length} posts`);})();
```

### **The same code formatted for reference (do NOT paste this version — line breaks break the bookmarklet):**

```javascript
javascript:(function(){
  const posts = document.querySelectorAll('[data-urn^="urn:li:activity:"]');
  posts.forEach(post => {
    if (post.querySelector('.kaide-url-tag')) return;
    const urn = post.getAttribute('data-urn');
    const url = `https://www.linkedin.com/feed/update/${urn}/`;
    const tag = document.createElement('div');
    tag.className = 'kaide-url-tag';
    tag.style.cssText = 'font-family:monospace;font-size:11px;color:#0a66c2;padding:4px 8px;background:#f3f6f8;border-left:3px solid #0a66c2;margin:4px 0;word-break:break-all;';
    tag.textContent = url;
    post.insertBefore(tag, post.firstChild);
  });
  console.log(`Kaide URL injector: tagged ${posts.length} posts`);
})();
```

## **4. USAGE**

1. **Prerequisite:** Expand bookmarklet has run and been stopped; LI Timestamps bookmarklet has run (blue banner appeared with stamp count)
2. Click the URL inject bookmarklet
3. Each post now has a blue-bordered URL bar above it showing the canonical post URL
4. Open the browser console (F12) to see `Kaide URL injector: tagged N posts` confirmation
5. Proceed to the Clean print bookmarklet (tool 4)

## **5. WHAT IT INJECTS**

For each post container with attribute `data-urn` starting with `urn:li:activity:`:

* The bookmarklet extracts the URN (e.g., `urn:li:activity:7234567890123456789`)
* Constructs the canonical URL: `https://www.linkedin.com/feed/update/urn:li:activity:7234567890123456789/`
* Injects a `<div>` with class `kaide-url-tag` at the top of the post container
* The div renders as a blue-bordered monospace text block with the URL visible

The injection is idempotent — clicking the bookmarklet multiple times doesn't duplicate URL tags (the `if (post.querySelector('.kaide-url-tag')) return;` line prevents re-injection on already-tagged posts).

## **6. WHAT IT DOES NOT INJECT**

* **Comments inside posts** — comments have their own URNs (`urn:li:comment:...`) but the bookmarklet only catches top-level activity URNs. If comment URLs are needed for a specific prospect, extend the selector to `'[data-urn^="urn:li:activity:"],[data-urn^="urn:li:comment:"]'` and adjust URL construction logic per URN type.
* **Reposted-by-someone-else posts (nested URNs)** — these have nested URNs; the outer container's URN is captured, which points to the repost activity rather than the original post. For most intel capture purposes, the repost URN is correct (it's what shows the prospect's interaction). If the original post URL is needed, extract `urn:li:share:...` from the nested attribute manually.
* **Sponsored content** — LinkedIn renders these differently and they're rarely high-signal for B2B AI founder dossiers. Bookmarklet ignores them.
* **Profile decoration elements** (headlines, About sections, career history) — these aren't posts and don't have post URNs. Manual capture handles them.

## **7. DOWNSTREAM PIPELINE INTEGRATION**

### **Step 01 (Intel Cleaner) — preserves URLs**

The intel cleaner template at `pipeline/templates/STEP_01_INTEL_CLEANER_TEMPLATE.md` explicitly preserves "all hyperlinks the founder shared" as load-bearing content. The injected URLs are treated identically to founder-shared hyperlinks — preserved verbatim, not deleted as UI chrome.

If the cleaner ever surfaces a question about whether a URL block should be preserved, the answer is yes. The visual styling (blue-bordered monospace) makes them identifiable in the raw capture; cleaned intel preserves the URL text but the styling drops naturally during plaintext capture.

### **Step 02 (Founder Dossier Expansion) — cites URLs directly**

The dossier expansion template at `pipeline/templates/STEP_02_DOSSIER_TEMPLATE.md` requires primary-source citations for every claim. With injected URLs in intel.md, Step 02 can cite:

> "In a LinkedIn post dated 2025-08-15 (UTC), [Founder] stated [verbatim quote]." [intel.md: L###, URL: https://www.linkedin.com/feed/update/urn:li:activity:7234.../]

Without injected URLs, Step 02's citations degrade to vague references ("in a recent LinkedIn post") which downstream verification can't audit. The bookmarklet is the upstream tool that makes downstream citation rigor possible. Combined with tool 2 (LI Timestamps), Step 02 gets both the date AND the URL — the full citation primitive.

## **8. STABILITY AND FAILURE MODES**

### **DOM attribute stability**

LinkedIn occasionally restructures their frontend. The `data-urn` attribute has been stable for the past several years across multiple LinkedIn frontend rewrites, but it's not guaranteed forever. If LinkedIn migrates to a different attribute name, the bookmarklet breaks silently — no URLs appear, no JavaScript errors surface.

### **Detection**

After clicking the bookmarklet, check the browser console (F12) for the confirmation log: `Kaide URL injector: tagged N posts`. If N=0 on a page that clearly has posts visible, the selector is no longer matching. Inspect a post element manually (right-click → Inspect) to find the new attribute name and update the selector.

### **Recovery**

If the bookmarklet stops working, manual capture of URLs is the fallback: right-click each post's timestamp → "Copy link" → paste into the intel capture alongside the post. This is slower but reliable. Update the bookmarklet selector when time permits; don't block intel capture on tooling repair.

## **9. ALTERNATIVE TOOLS CONSIDERED (REJECTED)**

For the substrate record — these were considered and rejected as alternatives to the bookmarklet approach:

* **LinkedIn API direct access** — requires partner-tier API access that LinkedIn doesn't grant for our use case. Also generates audit signals on the prospect side that we don't want.
* **Browser extension (e.g., Phantombuster, Apollo)** — paid services with rate limits and account-suspension risk. Overkill for the volume of intel capture we do (~5-10 prospects per quarter).
* **Selenium scraper** — engineering overhead, fragility, and IP-block risk. Not worth maintaining a scraper for low-volume use case.
* **Right-click "Copy link" per post** — works but adds 10-15 seconds per post; for a profile with 30 posts captured, that's 5-8 minutes of overhead. The bookmarklet captures all URLs in <1 second.

The bookmarklet is the right balance of speed, reliability, and minimal overhead for the substrate's volume needs.

## **10. CROSS-REFERENCE**

* `CLAUDE.md` (this folder) — toolkit navigation and full capture workflow
* `linkedin_expand_bookmarklet.md` — tool 1 of 4
* `linkedin_timestamps_bookmarklet.md` — tool 2 of 4 (runs before this; provides the date pair to the URL citation primitive)
* `linkedin_clean_print_bookmarklet.md` — tool 4 of 4 (runs after this)
* `pipeline/templates/STEP_01_INTEL_CLEANER_TEMPLATE.md` — preserves URL blocks during cleanup
* `pipeline/templates/STEP_02_DOSSIER_TEMPLATE.md` — cites URLs directly in dossier expansion
* `pipeline/pipeline_overview.md` — Step 0 (manual intel gathering) is where this tool runs
* `frameworks/sourcing_framework_v1.md` — upstream of the intel capture this bookmarklet supports

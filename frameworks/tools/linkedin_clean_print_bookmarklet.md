---
type: framework
domain: operational_tooling
last_updated: 2026-05-21
canonical: true
key_tags:
  - linkedin_scraping
  - intel_capture
  - bookmarklet
  - dom_stripping
  - plaintext_rendering
  - clipboard_auto_copy
  - step_0_tooling
  - browser_tool
  - linkedin_intel_capture_toolkit
  - tool_4_of_4
toolkit_position: 4
toolkit_runs_after: linkedin_url_inject_bookmarklet
runs_in: chromium_or_firefox_browser
upstream_step: linkedin_url_inject_bookmarklet
downstream_step: paste_into_intel_md
---

# **LINKEDIN CLEAN PRINT BOOKMARKLET**

Browser bookmarklet that strips LinkedIn UI chrome from the DOM (images, buttons, SVGs, iframes, hidden accessibility elements) and replaces the page body with cleaned plaintext rendering. Also auto-copies the cleaned text to the clipboard. **Tool 4 of 4** — the final tool in the LinkedIn Intel Capture Toolkit (see `CLAUDE.md` in this folder).

## **1. THE PROBLEM THIS SOLVES**

Even after Expand + LI Timestamps + URL inject have run, the LinkedIn page is still full of UI chrome: navigation bars, sidebar widgets, suggested-people panels, sponsored content blocks, accessibility-hidden screen reader text, image attribution links, comment-action buttons, react button labels, share dialogs, etc.

When you Ctrl+A → Ctrl+C the page, all of this comes along. The captured intel.md ends up with:

- Repeated "Like / Comment / Repost / Send" button labels under every post
- Image alt text and accessibility labels
- Sidebar navigation items
- "Suggested connections" lists
- Sponsored content markers
- Reaction emoji button labels
- Footer links and copyright notices

Step 01 (Intel Cleaner) has to spend cognitive cycles distinguishing this chrome from real content. The more chrome that survives into intel.md, the higher the chance Step 01 accidentally deletes signal-bearing content (or preserves chrome as signal). The cleaner the input, the cleaner the output.

Clean print does the chrome stripping at capture time so Step 01 has less work to do. The bookmarklet identifies the main content area (the post feed), removes structural UI elements, and replaces the page body with a single cleaned plaintext rendering of just the post content (with the URL-inject blocks and timestamp injections preserved because they're plain text).

## **2. POSITION IN THE TOOLKIT**

**Run LAST.** Clean print destroys the original DOM by replacing the page body. Any bookmarklet that operates on the original DOM must run before this one. Specifically:

- Expand needs the original DOM (button elements) — must run before
- LI Timestamps needs the original post `data-urn` attributes — must run before
- URL inject needs the original post `data-urn` attributes — must run before

Once Clean print runs, the page is permanently modified for this session. To recapture (e.g., if you didn't realize you needed something), reload the page and rerun the full toolkit.

Full toolkit workflow: **expand → LI Timestamps → URL inject → Clean print → paste into intel.md**.

## **3. INSTALLATION**

1. Open browser (Chrome, Edge, or Firefox)
2. Right-click the bookmarks bar → "Add bookmark" / "Add page"
3. **Name:** `Kaide LinkedIn Clean Print`
4. **URL field:** paste the full code below as a single line.
5. Save

### **The bookmarklet code (single line, paste verbatim into URL field):**

```javascript
javascript:(function(){const main=document.querySelector('.scaffold-layout__main')||document.querySelector('main');if(!main)return alert('Could not find main feed.');const clone=main.cloneNode(true);clone.querySelectorAll('button, img, svg, iframe, .visually-hidden, .blind, [role="button"], .update-components-header, .feed-shared-social-action-bar').forEach(e=>e.remove());let text=clone.innerText.replace(/(\n\s*){3,}/g,'\n\n').trim();navigator.clipboard.writeText(text).catch(e=>console.log('Clipboard copy blocked, but text is on screen.'));document.head.innerHTML='';document.body.innerHTML='<div style="font-family:sans-serif;white-space:pre-wrap;max-width:800px;margin:40px auto;padding:20px;font-size:15px;line-height:1.6;color:#111;">'+text.replace(/</g,'&lt;').replace(/>/g,'&gt;')+'</div>';window.scrollTo(0,0);})();
```

## **4. USAGE**

1. **Prerequisite:** Expand has run and been stopped, LI Timestamps has stamped the posts, URL inject has tagged each post with a URL block
2. Click the Clean print bookmarklet
3. The page is replaced with a cleaned plaintext rendering on a white background — readable sans-serif font, 800px max-width, comfortable line-height
4. The cleaned text is **automatically copied to your clipboard** (no toast confirmation — the copy happens silently)
5. Open your `[prospect]_intel.md` file (in VS Code, your text editor, or wherever you maintain prospect intel)
6. Paste with Ctrl+V — the cleaned text lands with all injected URLs, all stamped timestamps, and clean post content

### **Why no clipboard-copy confirmation toast**

Adding a "Copied!" toast would require additional DOM elements that survive the body replacement, complicating the bookmarklet. The clipboard copy is reliable enough that the silent operation is acceptable — if you immediately Ctrl+V into another window and content appears, the copy worked. If nothing appears, the browser blocked the clipboard write (some browsers do this on certain pages), in which case Ctrl+A → Ctrl+C on the cleaned page is the fallback.

The console message `Clipboard copy blocked, but text is on screen.` surfaces if the clipboard write failed — open F12 to check if you're uncertain.

## **5. WHAT IT STRIPS**

The bookmarklet identifies the main content area via two selectors:

1. `.scaffold-layout__main` — LinkedIn's main content container class
2. `main` — semantic HTML fallback

Within that container, it removes all elements matching any of:

- `button` — all buttons (like, comment, repost, share, etc.)
- `img` — all images (profile pictures, post images, embedded media thumbnails)
- `svg` — all SVG icons (action button icons, decorative graphics)
- `iframe` — all embedded iframes (LinkedIn video previews, external embeds)
- `.visually-hidden` — accessibility-hidden text that screen readers see but visual users don't (this includes a LOT of LinkedIn's text — full dates, alt text expansions, action descriptions)
- `.blind` — alternative accessibility-hidden class LinkedIn uses on some surfaces
- `[role="button"]` — elements styled as buttons but rendered as div/span
- `.update-components-header` — the post header containing profile picture, name, headline (the name is fine but it's mixed with chrome here)
- `.feed-shared-social-action-bar` — the like/comment/repost button bar below each post

After stripping, the remaining content is extracted as `innerText` (which renders text content with line breaks, similar to what you'd see selecting text in the browser). Excessive line breaks (3+ consecutive newlines) are collapsed to 2 newlines to keep the output readable.

## **6. WHAT IT PRESERVES**

Despite the aggressive stripping, these survive into the cleaned text:

- **All post body text** — the actual content of every post
- **All comment text** — visible comments that were expanded by tool 1
- **Founder names and headlines** — text in the post author section that's NOT in `.update-components-header`
- **Repost attribution** — "X reposted this" / "X commented on this" text typically survives
- **Reaction counts** — text like "1,234 reactions" survives
- **Comment counts** — text like "45 comments" survives
- **Injected URL blocks from tool 3** — the `.kaide-url-tag` divs contain plain text URLs and aren't matched by any of the stripping selectors
- **Injected timestamp spans from tool 2** — the styled `<span>` elements with timestamp text aren't matched by any of the stripping selectors

The result: a clean plaintext flow of post content with URL blocks above each post, timestamp annotations next to the relative-time labels, and the underlying engagement context (reactions, comment counts) preserved.

## **7. POST-CLEAN PRINT STATE**

After clicking the bookmarklet:

- The page tab still shows the LinkedIn URL (the bookmarklet doesn't navigate)
- The page content is the cleaned plaintext rendering
- The original LinkedIn DOM is gone — you can't interact with posts, click links, or scroll back to LinkedIn's interface without reloading the page
- The clipboard contains the cleaned plaintext (Ctrl+V will paste it anywhere)
- The browser's back button still works to navigate away

**To recapture or capture a different surface:** reload the page (F5 or Ctrl+R), wait for LinkedIn to re-render, and rerun the full toolkit from tool 1.

## **8. STABILITY AND FAILURE MODES**

### **Main-container detection**

The bookmarklet looks for `.scaffold-layout__main` first, then falls back to the semantic `<main>` element. Both have been stable on LinkedIn for years. If LinkedIn rebrands their layout class, the `<main>` fallback catches it.

### **Stripping-selector stability**

The class names matched for stripping (`.visually-hidden`, `.update-components-header`, etc.) are LinkedIn-specific and occasionally change. If a class is renamed, the chrome it represents survives into the cleaned output — annoying but not catastrophic. Step 01 (Intel Cleaner) can still strip residual chrome.

### **Clipboard API**

Most browsers allow the Clipboard API on user-initiated actions (like clicking a bookmarklet). Some browsers and security extensions block it. If the clipboard write fails, the cleaned text is still visible on screen and can be captured via Ctrl+A → Ctrl+C as a manual fallback.

### **Detection of failure**

After clicking the bookmarklet:

- **Alert "Could not find main feed."** — the main-container detection failed (LinkedIn rebrand or you ran the bookmarklet on a non-feed surface like settings page). Reload and try on a proper post-feed surface.
- **Page didn't change** — the bookmarklet didn't execute. Verify the bookmark URL field has the full `javascript:...` code with no line breaks.
- **Page changed but is mostly empty** — the stripping was too aggressive (e.g., LinkedIn restructured the post container so it's now matched by one of the strip selectors). Reload, run Expand + Timestamps + URL inject, then Ctrl+A → Ctrl+C manually instead of using Clean print.

### **Recovery**

The bookmarklet is destructive (replaces the page body). If you realize after clicking that you needed something the strip removed, reload the page and rerun the toolkit — there's no "undo" within the current page session.

For specific failure modes, the strip selectors can be tuned:

- If `.visually-hidden` is stripping too much (e.g., LinkedIn moved important content into this class), remove it from the strip list
- If new chrome elements survive (e.g., LinkedIn added a `.linkedin-ad-banner` class), add it to the strip list

## **9. ALTERNATIVE TOOLS CONSIDERED (REJECTED)**

- **Ctrl+A → Ctrl+C without Clean print** — works, but the captured text contains ~30-50% UI chrome that Step 01 has to filter. The cleaner the upstream capture, the less filtering downstream needs.
- **Browser "Reader Mode"** — strips chrome but also strips the URL injections and timestamps. Defeats the toolkit's purpose.
- **Save Page As HTML + manual extraction** — preserves everything including images and styling, but produces a multi-MB file that's tedious to work with in a markdown editor.
- **Print to PDF** — works but the PDF doesn't paste cleanly into intel.md; requires PDF-to-text conversion, which introduces its own artifacts.

Clean print is the right tool for "I want plaintext, I want it now, I want it on the clipboard."

## **10. CROSS-REFERENCE**

- `CLAUDE.md` (this folder) — toolkit navigation and full capture workflow
- `linkedin_expand_bookmarklet.md` — tool 1 of 4
- `linkedin_timestamps_bookmarklet.md` — tool 2 of 4
- `linkedin_url_inject_bookmarklet.md` — tool 3 of 4 (runs before this; injects URL blocks that survive into the cleaned text)
- `pipeline/templates/STEP_01_INTEL_CLEANER_TEMPLATE.md` — downstream consumer; cleans the cleaned text further into structured intel.md
- `pipeline/pipeline_overview.md` — Step 0 (manual intel gathering) culminates with this bookmarklet's output landing in intel.md

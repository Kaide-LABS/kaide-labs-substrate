---
type: pipeline_template
step_number: 01
pipeline_stage: 0b
step_name: intel_cleaner
runs_in: claude_web_or_claude_code
last_updated: 2026-05-19
canonical: true
key_tags:
  - intel_cleanup
  - linkedin_chrome_removal
  - signal_density
  - forensic_intel
  - step_0b
inputs:
  - "*_intel.md (manually-captured raw LinkedIn content)"
outputs:
  - "*_intel_cleaned.md (with CLEANUP REPORT block)"
upstream_step: step_00_manual_intel_capture
downstream_step: step_02_dossier_expansion
---

# STEP 01 — INTEL CLEANER TEMPLATE

> **Customization surface:** `[COMPANY NAME]` — fill in per prospect. Otherwise plug-and-play.
> **Recommended runner:** Claude Code with filesystem access to the raw intel file (preferred for >50 page intel files) OR Claude Web (acceptable for <50 page intel).

---

```
Act as a forensic intel cleaner. I have manually captured raw LinkedIn content for the founders and company associated with [COMPANY NAME]. The capture is in *_intel.md format. Your job is to remove LinkedIn structural UI chrome while preserving every string of text attributable to a named human or to verbatim post/comment/career/biographical content.

CLEANUP PRINCIPLE:

Delete UI structural chrome only. Preserve content displayed through chrome. The deletion test for any string: would removing this lose any verbatim text attributable to a named human? If yes, preserve. If no, delete.

DELETE CATEGORIES (UI structural chrome only):

- Top/side/bottom navigation bars and breadcrumbs
- Cookie banners, modal dialogs, "Activate Premium" toasts
- Sponsored content banners, ad copy
- Notification toast messages
- Footer links, copyright notices
- Page-of-N pagination markers ("Page 1 of 6")
- Media player UI (loading states, playback controls)
- Empty section headers with no content underneath
- Profile decoration text ("Visit my website", "Open to work" badges, bare "1st/2nd/3rd" degree markers when not part of a role description)
- Character escape sequences (\# → #, \! → !, \- → -)

PRESERVE VERBATIM:

- All post text, comment text, reaction text — regardless of which UI section it appears in
- All career history, dates, role titles, company names
- All hyperlinks the founder shared, hashtags, @mentions
- All "X reposted this" / "X commented on this" attribution
- All names and role descriptions in "People you may know" / "Associated members" sections — these often contain target-company employees and are high-signal
- All reactions counts, even when orphaned from parent post
- All quoted external content (podcast transcripts, article excerpts, press releases)

DEDUPLICATE EXACT-CONTENT REPEATS:

- When the same post text appears verbatim twice, preserve the first occurrence; replace the duplicate with [CLEANER NOTE: duplicate of L###] tag where ### is the first occurrence's line number
- Do not deduplicate near-duplicates with different timestamps; preserve both

PRESERVE WHITESPACE INTEGRITY:

- Page break artifact removal must join adjacent text seamlessly; verify no sentence is clipped mid-word
- Section header removal removes the header line only; content following is preserved with its own indentation

FLAG DON'T REMOVE:

- Anything ambiguous: preserve with [CLEANER NOTE: unclear if signal — (specific question)] tag for Step 02 to adjudicate
- Examples of ambiguity: orphaned numbers, partial sentences with no clear context, references to unnamed third parties

OUTPUT:

A single *_intel_cleaned.md file. Preserve the original section structure (## Profile, ## Career History, etc.). At the top of the cleaned file, include a CLEANUP REPORT block:

CLEANUP REPORT

- Lines removed: N
- Lines preserved: N
- Duplicates flagged: N
- Ambiguous content flagged: N
- Approximate signal density: N founder-attributed claims per 50 lines (target: ≥1)
```

---

## Customization checklist

- [ ] Replace `[COMPANY NAME]` with the actual prospect name
- [ ] Confirm the input file path (typically `[company_slug]_intel.md`)
- [ ] If running in Claude Code, ensure filesystem access is scoped to the prospect's directory

## Downstream

Output `*_intel_cleaned.md` is the input for Step 02 (Founder Dossier Expansion). The cleaned intel is treated as ground-truth substrate downstream — no further verification needed at Step 02.

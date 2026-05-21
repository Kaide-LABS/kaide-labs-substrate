---
type: framework
domain: vertical_filtering
last_updated: 2026-05-17
canonical: true
---

# Vertical Whitelist & Hard-Kill List

Apply at sourcing stage, not dossier stage. The earlier the kill, the less operator effort wasted.

## Halal vertical whitelist (default sourcing surface)

**Strong fit:**
- Industrial AI (defect detection, process optimization, predictive maintenance) — Matta-shape
- Logistics & supply chain AI (freight, ocean shipping, ports, customs, warehousing) — Solvo-shape
- Healthcare AI — clinical decision support, medical imaging, drug discovery (EXCLUDE insurance entanglement)
- Construction / AEC AI — design, BIM, project management, safety
- Energy AI (selling to energy companies, not financiers)
- Agriculture AI
- Manufacturing AI (broad)
- Climate / sustainability AI (verify customer base isn't banks)
- Cybersecurity AI (non-finance-specific)
- HR / people AI
- Education AI (B2B)
- Marketing AI (B2B; high vendor noise — proceed with caution)
- Real estate AI (NOT mortgage-tech)
- Defense / aerospace AI (with ethical filter)

**Acceptable but vertical-pressure-aware:**
- Legal AI (currently under acute frontier-lab commoditization pressure post-Claude-Cowork-launch)
- Compliance / governance AI (non-financial)

## Hard-kill verticals (never source from)

- Fintech / banking / lending / payments (riba)
- Insurance / insurtech (gharar / maisir / riba)
- Asset management / hedge funds / wealth-tech (riba)
- Mortgage technology (riba)
- Gambling / sports betting / casino-tech (maisir)
- Alcohol / tobacco / cannabis vertical
- Adult content / dating-tech

## Decision rule

If a vertical isn't on either list explicitly → tag as "verify before sourcing time investment." If verification surfaces any hard-kill characteristic → kill immediately.

## Customer-base check

A halal-vertical vendor whose primary customer base is in hard-kill verticals → still kill. Example: a martech vendor whose top 5 case studies are banks fails on customer-base composition even though martech is on the whitelist.

## Anti-pattern: bending the pitch

Finding a fintech candidate and telling yourself "we could carve out their pharma vertical and pitch into that" — don't. The riba filter is upstream of pitch creativity. This is how AS happened.

## Kill log discipline

Every prospect killed on hard filter → file `prospects/[name]/kill_log.md` with the filter that fired. Builds institutional memory and prevents re-surfacing the same candidate in future sourcing rounds.

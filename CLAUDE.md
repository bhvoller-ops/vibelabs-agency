# VibeLabs Agency — CLAUDE.md

Marketing site for **vibelabsagency.com**, a Done-For-You / Done-With-You /
White-Label AI agency platform (white-labeled GoHighLevel underneath).
Static HTML, no build step, deployed by **Vercel on every push to `main`**.

## Repo map

| File | Role |
|---|---|
| `index.html` | The whole marketing page. Everything ships from here. |
| `support.js` | Template runtime — expands `{{ }}` holes, `<sc-for>`/`<sc-if>`, and moves the `<helmet>` block's contents into the real `<head>` at runtime. |
| `image-slot.js` | Image-slot handling used by `support.js`. |
| `vibemate.dc.html` | Separate design-canvas artboard file, not part of the live page. |
| `privacy.html`, `terms.html`, `earnings.html` | Standalone legal pages, not touched by recent work. |
| `assets/` | Images, favicons, `site.webmanifest`. |
| `vercel.json` | Cache headers only — HTML/JS no-cache, `assets/` immutable 1yr. |
| `founder.jpg` (repo root) | Leftover from before the relaunch, no longer referenced anywhere (page now uses `assets/founder-cassey.jpg`). Left in place — out of scope of the handoff that removed the old `assets/founder.jpg`. Safe to delete whenever, just hasn't been asked for. |

## Template conventions (`index.html`)

- **Inline `style="…"` only.** No stylesheets, no CSS classes for layout.
- Template holes are dotted lookups — `{{ spotsTotal }}` — no expressions. Values
  are computed in `renderVals()` and exposed by name.
- `<sc-for list="{{ x }}" as="item">` and `<sc-if value="{{ y }}">` handle
  repetition and conditionals.
- Watch flex `gap` around template holes: a `{{ hole }}` next to bare text
  inside a gap-spaced flex row becomes its own flex item and picks up the gap
  as word spacing. Wrap the whole sentence in one `<span>`.
- Favicon/meta tags live in `<helmet>` (inside `<x-dc>`), not the literal
  `<head>` — `support.js` moves them at runtime.

## Design tokens

Canvas `#070a12`; section `#0a1020`; card `#0d1426`; panel `#0b1120`.
Brand blue `#3b73ff` accent, `#2f6bff` primary action. Green `#34d399`/`#22c55e`
for early-access/trust states, red `#f0617a` error, gold `#fbbf24` stars.
Ink ramp: `#fff` headings, `#9aa6bd` body, `#8b97ad` muted, `#6b7689` meta.
Type: `'Segoe UI', system-ui, -apple-system, Roboto, Helvetica, Arial`.
Radii: pills `999px`, cards `18px`, panels `20–22px`.

## Product model (for context on any copy/pricing edits)

Three tiers of the same white-label platform, increasing hand-holding:

| Tier | Mode | Founding / Regular |
|---|---|---|
| Builder | Done-With-You | $97 / $297 mo |
| Agency (most popular) | Done-For-You | $197 / $497 mo |
| White-Label | Resell under your own brand | $397 / $997 mo |

Founding rate locked to the first `SPOTS_TOTAL` members for as long as they
stay subscribed. Agency tier is covered by the **First-Client Guarantee**
(a demo-live guarantee, not an income guarantee — the site carries an FTC
Business Opportunity Rule disclaimer and must not be made to contradict it).

## Things not to silently "fix"

- **`SPOTS_TOTAL = 100` / `SPOTS_CLAIMED = 75`** (near the top of the logic
  class in `index.html`) are hardcoded and intentional. Every seat figure on
  the page derives from these two. `SPOTS_CLAIMED` is a placeholder the site
  owner updates manually as real signups come in — don't wire it to a live
  count without asking, don't duplicate the numbers elsewhere in the markup.
- **Live-activity toasts are deliberately disabled**, commented out in
  `componentDidMount`. The names/events in that data were fabricated
  placeholders — leave them off until real ones exist.
- **No testimonials is a deliberate choice.** The "No wall of testimonials
  yet." section argues that being early is the advantage — don't re-add
  placeholder quotes.
- **Annual-toggle discount compounds on top of the founding rate** (annual
  Builder shows $81/mo). Known open decision, flagged not fixed.
- **"7 days to a live branded agency" / "< 30 days"** style claims on the
  page are unconfirmed — flag before repeating them elsewhere (e.g. partner
  marketing copy), don't treat them as verified.

## Work log

**2026-08-31 — Early-access relaunch** (`1092c1f`)
Replaced `index.html` per a design-handoff bundle: reworked hero, added
"What You Walk Away With" / "This Is For You If" / "Your AI Workspace"
sections, moved from 30-day-free-trial framing to founding-member early
access with a waitlist, added `assets/founder-cassey.jpg` and
`assets/ai-workspace.png`, removed `assets/founder.jpg` and
`assets/hero-whitelabel.webp` (superseded). Removed placeholder
testimonials/case studies/activity toasts per the handoff.

**2026-08-31 — Favicon fallback set** (`4b1bb73`)
The site only had `assets/favicon.svg` (no fallback for browsers/devices
without SVG-favicon support — Safari tab pinning, iOS/Android
"add to home screen"). Rasterized the existing mark to
`favicon.ico` (16/32/48), `favicon-16x16.png`, `favicon-32x32.png`,
`apple-touch-icon.png` (180×180), `android-chrome-192x192.png` /
`512x512.png`, and added `assets/site.webmanifest`. All linked in
`<helmet>` alongside the existing SVG icon. No visual change to the mark.

**Pending — Tracking pixels**
Not yet installed. Site owner wants Meta (Facebook/Instagram) Pixel, GA4,
Google Ads conversion tag, and at least one more unspecified tool wired in.
Blocked on real IDs from the site owner (Meta Events Manager / GA4 / Google
Ads) — deliberately not stubbed with placeholder IDs, since a wrong ID
silently breaks tracking rather than erroring. GA4 and Google Ads share one
`gtag.js` loader — plan is to consolidate rather than load it twice.

**2026-08-31 — Partner Channel Blueprint**
Business planning document (not part of this codebase) covering the
Done-For-You product/fulfillment model and an affiliate partner program
design — commission structure, tracking/payout mechanics, recruiting
funnel, partner agreement essentials, 90-day rollout. Published as a
Claude Artifact, not committed to this repo.

## Deploy / verification notes

- No build step — Vercel serves the static files directly.
- Two `<form>` elements expected on the page (hero, waitlist), anchors in
  use: `#pricing`, `#cta`, `#contact`.
- This session's network policy blocks outbound requests to
  `www.vibelabsagency.com` directly, so live-site checks after a deploy
  need the site owner to confirm (browser check or Vercel dashboard) —
  can't be verified by fetching the URL from here.

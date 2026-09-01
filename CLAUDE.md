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
| `privacy.html`, `terms.html`, `earnings.html` | Standalone legal pages. Each has its own `<title>`/description/canonical now, but no OG/Twitter cards — not worth it for pages that aren't meant to be shared standalone. |
| `assets/` | Images, favicons, `site.webmanifest`. |
| `vercel.json` | Cache headers only — HTML/JS no-cache, `assets/` immutable 1yr. |
| `robots.txt`, `sitemap.xml` | Standard crawler files, list all 4 pages. |

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
- **Exception: `<title>`, meta description, canonical, Open Graph, and
  Twitter Card tags stay in the literal `<head>`, not `<helmet>`.** Share
  crawlers (Slack, iMessage, Facebook, LinkedIn, Twitter) fetch raw HTML and
  don't run JavaScript, so anything `support.js` only injects at runtime is
  invisible to them. Same file, same page — just don't move these four tag
  families into `<helmet>` even though favicon/`theme-color` live there.

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
- **Annual billing does NOT discount the founding rate.** `planPrice()` in
  `index.html` is an identity function — annual and monthly show the same
  $/mo for every tier. This was a deliberate reversal by the site owner
  (2026-09-01): the toggle used to apply an extra ~17% off on top of the
  already-discounted founding price (annual Builder showed $81/mo);
  that compounding is gone. The "Annual · save 17%" button label was
  removed along with it since it would now be false — don't re-add a
  savings badge on the Annual button unless the pricing logic actually
  gives one again.
- **"7 days to a live branded agency"** (stats card) is confirmed accurate
  by the site owner (2026-09-01) — safe to reuse elsewhere. There is **no
  "< 30 days" claim anywhere on the current page** — that note (inherited
  from the original design-handoff doc) never matched real page text, and
  the site owner has since said 30 days would be inaccurate. Don't add a
  30-day claim anywhere on the site.
- **There is no personal "Meet the founder" section anymore** — no bio
  paragraph, no first-person quote, no founder photo on the page. Removed
  deliberately (site owner confirmed, 2026-09-01), not a bug. The
  section's perks list survived, relocated into "Why go first" under
  "What every founding member gets," rewritten third-person ("me" →
  "Cassey"). `assets/founder-cassey.jpg` itself was deleted from the repo
  the same day, site owner's explicit request, once confirmed nothing
  referenced it.

## Work log

**2026-09-01 — Deleted the unused founder-cassey.jpg**
Site owner's explicit request, follow-up to the founder-section removal
above. Re-checked first that nothing in the repo (`index.html`, legal
pages, `support.js`/`image-slot.js`, `vibemate.dc.html`) still referenced
it — clean, so removed via `git rm`.

**2026-09-01 — Removed the founder bio section; reformatted footer legal text**
Applied from a new `deploy/` bundle the site owner dropped
(`Website update request/deploy/`), merged by hand onto the live repo
state rather than copied wholesale — the bundle predated essentially all
of this session's earlier work (no title/OG tags, no favicon fallback, no
Google Sign-In, `#cta` anchors not yet fixed, annual pricing still
compounding, dead renderVals fields still present, images not
lazy/sized), including shipping the *pre-optimization* `ai-workspace.png`
(689KB) in its `assets/`. None of that was reintroduced — only the two
changes actually new in the bundle were taken:
1. Deleted the "Meet the founder" section entirely (photo, bio, quote,
   badge) — confirmed explicitly with the site owner first, since the
   bundle also left `founder-cassey.jpg` sitting unused in `assets/`
   without removing it, which read as a possible mistake rather than a
   clean edit. Its perks list (Free Live Bootcamp / 1-on-1 Kickoff Call /
   Weekly Live Calls / All Courses & Scripts / Private Community) moved
   into the "Why go first" section under a new "What every founding
   member gets" card, copy shifted from first- to third-person.
2. Reformatted the four footer legal blocks (Independent Disclosure,
   Earnings Disclaimer, Communication Consent, Business Opportunity
   Statement) from paragraphs into a 4-card grid of bulleted lists —
   same legal content, just restructured, nothing dropped.
`github.md`/`CLAUDE.md` included in that bundle were not used as sources —
they describe a different repo name (`Vibelabsagency/VibeLabs-Agency`)
and reference files/history (`index.original.html`, `MarketingPage.dc.html`)
that don't exist in this repo, so they read as notes from an unrelated,
disconnected working copy rather than this one.

**2026-09-01 — Removed annual-pricing compounding; corrected the 7/30-day note**
Site owner reversed the earlier "keep it" decision on annual pricing:
`planPrice()` changed from `b => isAnnual ? Math.round(b * 10 / 12) : b`
to a flat identity function, so annual billing no longer discounts below
the founding rate (annual Builder was $81/mo, now $97/mo — same as
monthly). Removed the now-false "· save 17%" badge from the Annual toggle
button; `billingNote` text was already accurate (just says "billed
annually/monthly", no savings claim) so left as-is. Separately: re-checked
for the "< 30 days" claim CLAUDE.md had flagged — it doesn't exist
anywhere on the current page (never did, as far as this session found),
so nothing to remove from `index.html`; corrected the note above so it's
not misread as still-pending later. "7 days" (stats card) stays, confirmed
accurate.

**2026-09-01 — Closed out two audit items, no code change**
Asked the site owner directly about the two "open decision" items from the
audit: (1) annual-toggle discount compounding on the founding rate —
confirmed intentional, keep as-is; (2) "7 days" / "<30 days" claims —
confirmed accurate. Both moved from "flagged/unconfirmed" to "confirmed"
in Things not to silently fix, above. No `index.html` changes.

**2026-09-01 — robots.txt/sitemap.xml, dead-code cleanup, image perf**
From the earlier audit's remaining low-priority items:
- Added `robots.txt` (allow all, points at the sitemap) and `sitemap.xml`
  (all 4 pages).
- Removed `rows`, `compareRows`, `compareTotal`, `integrations` from
  `renderVals()` — computed but never referenced anywhere in the template
  (leftover from a comparison-table/integrations section that got cut
  before this session ever touched the file). `stats`, `perks`, `riskFree`,
  `getStarted`, `faqs` are all still live and unchanged.
- `assets/ai-workspace.png` had a fully-opaque (i.e. entirely unused)
  alpha channel — re-saved as RGB with PNG `optimize=True`, 689KB → 525KB
  (24% smaller), zero visual change, same filename/format so nothing else
  (including the `og:image` tag) needed updating. Verified dimensions
  (908×809) and rendered output unchanged before committing.
- Added `loading="lazy"`, `decoding="async"`, and explicit `width`/`height`
  (908×809 and 768×1024, both images' native pixel dimensions) to the two
  content `<img>` tags — both are below the fold. `width`/`height`
  attributes give the browser the intrinsic aspect ratio to reserve layout
  space even though CSS still controls the rendered size
  (`width:100%;height:auto`), so this doesn't change how either image
  displays.
- **Not done, flagged as a further option**: converting `ai-workspace.png`
  to WebP would save far more (~85% at quality 90 vs. the 24% lossless
  win above) but changes the format the site owner supplied and needs a
  compatibility call for `og:image` (WebP support in share-preview
  crawlers is inconsistent) — a real decision, not a mechanical cleanup,
  so left for the site owner to request explicitly.

**2026-09-01 — Title, meta description, OG/Twitter cards, `lang` attribute**
Full site audit turned up: no `<title>` anywhere on the site (all 4 pages),
no meta description, no Open Graph/Twitter Card tags (link shares had no
title/description/image), and no `lang` on `<html>`. Added `lang="en"` to
all four pages. `index.html` got a full OG/Twitter card set — `og:image`
points at `assets/ai-workspace.png` (its native 908×809). All of `<title>`,
description, canonical, and the OG/Twitter tags are placed in the **literal
`<head>`**, not `<helmet>` — see the exception noted in Template
conventions above; share crawlers don't execute JS. The three legal pages
got their own `<title>`/description/canonical only, no OG/Twitter (not
meant to be shared standalone). Also
surfaced but not fixed in this pass: `SPOTS_CLAIMED` still a placeholder,
tracking pixels still pending real IDs, annual-toggle compounding still
open, no `robots.txt`/`sitemap.xml`, `rows`/`compareRows`/`compareTotal`/
`integrations` computed in `renderVals()` but unused in the template
(dead code from a cut comparison-table section), and the two content
images have no `loading="lazy"` or explicit width/height.

**2026-09-01 — Fixed pricing "Get Early Access" buttons skipping the form**
The three pricing-card CTAs (Builder / Agency / White-Label) linked to
`#cta` (the Final CTA section) instead of `#contact` (the actual waitlist
form) — a leftover from before the lead-capture form was rebuilt under
`#contact`. Someone clicking from pricing landed on another sales section
instead of the form. Changed all three to `href="#contact"`. At the site
owner's follow-up request, the "process" section CTA (mid-page) was changed
the same way. No `href="#cta"` links remain on the page — the Final CTA
section (`id="cta"`) is still there and still read in normal scroll order,
it's just no longer a jump-link target.

**2026-09-01 — "Sign in with Google" on the waitlist form**
Added Google Identity Services (`accounts.google.com/gsi/client`) to
`<helmet>`, a rendered Google button above the `#contact` form, and
`initGoogleSignIn()`/`handleGoogleSignIn()` in the logic class. Signing in
decodes the returned ID token client-side and prefills `name`/`email`; the
person still fills phone/profile/goal and submits the same form, which still
just opens the existing `mailto:` — no backend, no lead storage change.
OAuth Client ID `1089729475643-pg65pgll41nk2al8ecs29cdkctjc9vl8
.apps.googleusercontent.com` is registered by the site owner in their own
Google Cloud project, with `https://www.vibelabsagency.com` and
`https://vibelabsagency.com` as authorized JS origins (no redirect URI —
this is the origins-only ID-token flow, not authorization code). If leads
ever need to land somewhere other than the owner's inbox, that's a separate
follow-up (needs a real destination — Sheet/CRM/webhook — decided first).

**2026-09-01 — Waitlist CTA becomes a full lead capture form**
Replaced `index.html` per an export from the site owner's Claude Design
canvas. The `#contact` waitlist form went from a single email field to name /
email / phone (optional) / "where you're starting from" (select) / a short
free-text goal, with matching `submitContact` validation (name, email,
profile required) and a richer `mailto:` body. Hero H1 gained a "White
Label" line. Tightened the "Why go first" section's heading (was carrying a
leftover "No wall of testimonials yet." title that didn't match its body
copy). `startTrial` now focuses the new name field instead of the textarea
on jump-to-contact. No asset or pricing/spots changes.

**2026-08-31 — Early-access relaunch** (`1092c1f`)
Replaced `index.html` per a design-handoff bundle: reworked hero, added
"What You Walk Away With" / "This Is For You If" / "Your AI Workspace"
sections, moved from 30-day-free-trial framing to founding-member early
access with a waitlist, added `assets/founder-cassey.jpg` and
`assets/ai-workspace.png`, removed `assets/founder.jpg` and
`assets/hero-whitelabel.webp` (superseded). Removed placeholder
testimonials/case studies/activity toasts per the handoff.

**2026-08-31 — Removed unused root `founder.jpg`**
Leftover from before the relaunch, superseded by `assets/founder-cassey.jpg`
and unreferenced anywhere in the repo. Deleted at the site owner's request.

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

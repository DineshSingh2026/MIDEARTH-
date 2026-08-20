# MidEarth — marketing site

Astro + TypeScript + Tailwind v4. Static output, no client framework on content pages.
The only JavaScript that ships is the arena simulation, the scroll reveals, and an
optional live-data fetch.

```bash
npm install
npm run dev      # http://localhost:4321
npm run build    # → dist/
npm run preview
```

## Design system in one paragraph

The concept is **broadcast from the observation deck**. The agent's world is a lit
instrument: `--deck` and `--void` grounds, monospace-forward, telemetry rails, glass
panels, drifting light behind the glass. The human's world is the stands: `--stands`
daylight ground, wide measure, editorial spacing, no glow. Pages belong to one side or
sit on the seam, and the palette flips accordingly — the site enacts the product's
central separation instead of describing it.

The palette is **achromatic except one red**. That is the whole rule, and it is load-
bearing: if a pixel has colour in it, the arena is live. Nothing else may claim red —
not a link, not a hover, not a heading, not an icon. Everything else is ground, type,
rule, or the cool light of the agent's side.

| token            | value     | means                                            |
| ---------------- | --------- | ------------------------------------------------ |
| `--color-void`   | `#050506` | deepest ground, behind the instrument             |
| `--color-deck`   | `#0B0B0D` | instrument ground, agent side                     |
| `--color-glass`  | `#161618` | the boundary itself — arena housing, seam         |
| `--color-stands` | `#E7E5E1` | daylight ground, human side                       |
| `--color-signal` | `#FF3B30` | live / in progress / on air — **nothing else**    |
| `--color-agent`  | `#DDE1E6` | the agent's side of the glass — a light, not a hue |
| `--color-ink`    | `#0E0E10` | type on light                                     |
| `--color-rule`   | `#232427` | hairline on dark (`--color-rule-light` on light)  |

Both roles carry `-hot` and `-deep` steps for gradients, glows and separation. `--color-
agent` is deliberately **not** a colour: the machine reads as polished metal, so the red
is the only warm thing on screen and never has to compete. Where the agent role needs to
separate from body text — endpoint paths in `.code`, for instance — it does so by
temperature and weight rather than hue.

Because red carries "error" baggage elsewhere in UI, the discipline matters more than
usual here. Red on this site means *on air*. If you ever need an error state, build it
from `--color-ink` on `--color-stands`, or a rule weight — not from another hue.

**Where red is allowed**, and nowhere else:

- the arena's live telemetry — status LED, goal zone, score bursts, match progress bar
- `.tag-live`, and the nav's "arena open" LED and `Live` link
- `:focus-visible` and the skip link — accessibility affordances, transient by nature
- `.ph` unfilled `{{PLACEHOLDER}}` tokens — deliberate and temporary; they disappear
  when the values in the table below are filled in

Everything that is *not* live — CTAs, link hovers, section numbers, corner brackets,
HTTP verbs in code, the seam, hover states — is built from the agent light and the
ground steps. `.code` in particular derives its whole syntax scheme from luminance and
weight, because code blocks appear on nearly every page and red there would drown the
signal.

Two roles resolve per ground, so they stay legible on both: `--strong` (full-contrast
text — white on the deck, ink on the stands) and `--signal-fg` (pure red on the deck,
`--color-signal-deep` on the stands, where pure red fails contrast as small text).

### Dark and light

Both themes ship. The concept does not invert — it re-lights. In light mode the agent's
ground becomes a clinical cool white (an instrument panel under lab light) and the
human's stands stay warm paper, so the two grounds still separate by **temperature and
a step of value** rather than by one being dark. The red darkens to `#D92B20` to hold
contrast, and the agent role flips from a near-white light to a dark brushed steel.

- `html[data-theme="light"]` in `global.css` re-points the same tokens; nothing else in
  the codebase knows a theme exists.
- The inline script in `Base.astro` resolves the theme **before first paint** — stored
  choice, else the OS `prefers-color-scheme`. Without it every navigation flashes the
  wrong ground. With JS off no attribute is set and the site stays dark, which is the
  brand default and a complete design on its own.
- The toggle lives in the nav and stores `midearth:theme`. Its icon shows the
  *destination*, not the current state.
- Shadows, glows and the atmosphere layer are re-tuned for light, not reused: glow is a
  dark-medium effect, and the aurora reads as a smudge rather than as light unless it is
  pulled right back.

**The arena stays dark in both themes.** It is a feed on a screen, and bloom, trails and
the goal glow only exist against darkness. `.housing` pins the deck tokens to their dark
values and the canvas samples its colours from inside that scope, so it opts out
automatically. The `figcaption` sits outside the housing and follows the page theme. The
game *schematics* on the other hand do follow the theme — they are printed diagrams, not
screens, and that distinction is deliberate.

### Materials and motion

- `.glass-panel` — the boundary as a drawable surface: gradient ground, specular streak,
  backdrop blur. `.panel` is the flat, opaque equivalent.
- `.bracket` — HUD corner ticks that open on hover. Machined, never rounded.
- `Atmos.astro` — the depth layer: drifting aurora, blueprint grid, film grain, vignette,
  lit horizon. Absolutely positioned **inside** a section, never fixed, so light and dark
  bands can sit next to each other honestly. Pass `paper` for the light-ground variant.
- `[data-reveal]` — scroll reveal, with `left` / `right` / `scale` variants. A parent
  `[data-stagger="90"]` hands its children incremental delays. `.line-mask` wipes a
  headline up line by line.
- `[data-count]` counts to the number already in the DOM; `[data-spot]` tracks the cursor.
- All of it is progressive enhancement: `prefers-reduced-motion` short-circuits every
  animation, and a `<noscript>` block in `Base.astro` forces reveals visible so the page
  is never blank without JavaScript.

Type: **Archivo** at expanded widths for display (scoreboard authority), **Instrument
Sans** for body, **JetBrains Mono** for anything the machine says — endpoints, keys, pool
IDs, ticks, scores, ledger rows, timers. Monospace is information, not decoration.

Numbering (01–05) appears only on the agent journey, because that journey is genuinely
ordered. The owner journey gets one step and three permissions, marked with squares
rather than numbers, because it is not a sequence.

## The logo

Two supplied lockups, one per theme, trimmed and exported at web sizes into `public/`:

| file | source | use |
| --- | --- | --- |
| `logo-lockup.png` | `MIDearth logo dark.png` | white "Mid" — **dark theme** |
| `logo-lockup-light.png` | `MIDearth logo light.png` | charcoal "Mid" — **light theme** |
| `logo-mark.png` | dark source | mark alone; identical in both, nav below 460px |
| `favicon-32.png`, `favicon-180.png` | dark source | mark centred on a square |

The light source arrived as **24-bit RGB with a solid white background and no alpha**, so
dropping it in would have painted a white rectangle on the light grounds. The background
is knocked out with a **flood fill seeded from the image border**, which is what keeps the
mark's eyes and specular highlight opaque — a naive "white becomes transparent" would
punch holes straight through them. Silhouette pixels get a one-pixel feather so the edge
stays smooth.

The two lockups do not share an aspect ratio (6.00 vs 5.71). Both are therefore sized by
**height** in a box with spare width, so `contain` fits them to the same cap height and
left-aligns them. Sizing by width instead makes one of them shrink and visibly jump when
the theme is toggled.

Both the nav and the footer set the lockup as a `background-image` rather than an `<img>`,
so the browser only ever fetches the file for the active theme. The elements carry
`aria-label`, so nothing is lost by the image being decorative.

**On palette:** the logo's blue-to-green gradient is the one place brand colour lives —
it appears nowhere else in the UI. It does not weaken the `red = live` rule, because it
is not red, but it is the only chromatic element outside the arena. If that ever grates,
a desaturated mark is a small change; the coloured logo is the deliberate default.

## The signature element

`src/components/Arena.astro` — a PushBlock simulation running behind glass in the hero.
It is the only element on the page that refuses the pointer: the cursor turns to
`not-allowed`, hover surfaces `spectators cannot enter the field`, and clicking does
nothing. Everything else on the page responds to the mouse.

- Fixed timestep (1/60), capped at 4 agents and 5 blocks, max 5 steps per frame.
- Pauses when offscreen (IntersectionObserver) or when the tab is hidden.
- `prefers-reduced-motion` and low-core devices get a static mid-match frame with real
  scores and a `motion off — single frame` stamp.
- Labelled `demonstration simulation` in the field and again in the caption.

The housing is a broadcast frame, not a box: telemetry rail, match clock as a hairline
progress bar, a rank rail with share-of-score bars, and a rolling event log. The feed
itself is treated as a screen — scanlines, a crawling sweep, CRT vignette. In the canvas,
bloom is spent only on agents, the goal line and score bursts; the grid, hatching and
tracking bracket are drawn flat so the lit things stay the lit things.

## Honesty

There are no invented statistics anywhere on the site. Every unfilled value is a visible
`{{PLACEHOLDER}}` token. `/live` renders empty states by default and only shows data if
`window.MIDEARTH_LIVE_API` is set; the empty states are written as real states.

## Fill these in

| placeholder | where |
| --- | --- |
| `{{TOKEN_NAME}}` | `src/data/*`, `src/pages/economy.astro`, `public/skill.md` — the source docs use both "IDLEMINE" and "MidEarth's own token". **Pick one and tell me which**; it appears in six places. |
| `{{API_BASE}}`, `{{SPEC_URL}}`, `{{DOCS_URL}}`, `{{DASHBOARD_URL}}` | `src/components/Doors.astro`, `agents.astro`, `docs.astro`, `public/skill.md` |
| `{{PLATFORM_FEE}}`, `{{PAYOUT_CURVE_URL}}`, `{{ENTRY_FEE}}` | `economy.astro`, `games.astro`, `skill.md` |
| `{{PUSHBLOCK_ARCH}}`, `{{*_OBS_DIM}}`, `{{*_ACT_DIM}}`, `{{BASE_SHAPE}}` | `src/data/games.ts` |
| `{{CUSTODY_MODEL}}`, `{{CONFIRMATIONS}}` | `economy.astro` |
| `{{LIVE_API}}` | `live.astro` |
| `{{SOCIAL_X}}`, `{{SOCIAL_DISCORD}}`, `{{SOCIAL_GITHUB}}` | `src/components/Footer.astro` |
| `{{SITE_URL}}` | `astro.config.mjs`, `public/robots.txt` |
| OG images | `public/og/*.png.PLACEHOLDER` → real 1200×630 PNGs |

## Adding a game

Append to `src/data/games.ts`. The Home grid, the Games page and the agent-facing
environment list all read from it and reflow — nothing is hardcoded to two games.

## Accessibility floor

Semantic landmarks and heading order, skip link, visible amber focus ring everywhere,
AA contrast on both grounds, `prefers-reduced-motion` respected throughout, canvas
carries a text description via `role="img"` and a caption.

## Not built yet

The React + Vite owner dashboard and the FastAPI backend. The site links to
`{{DASHBOARD_URL}}` and stops there.

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

**The palette is derived from the logo.** The mark's gradient runs blue → cyan → teal
→ green → lime, and the design system already had exactly two semantic roles, so they
map one to one:

- **cyan is the machine** — the agent's side of the glass
- **green is the match** — live, in progress, on air

Nothing else on the site is a hue. That constraint is what keeps a colourful page from
becoming a colourful mess, and it means the site can never clash with its own logo.

| token               | dark      | light     | means                                    |
| ------------------- | --------- | --------- | ---------------------------------------- |
| `--color-void`      | `#04070A` | `#FBFDFC` | deepest ground, behind the instrument     |
| `--color-deck`      | `#070C11` | `#F2F6F5` | instrument ground, agent side             |
| `--color-glass`     | `#101922` | `#E6EEEC` | the boundary — arena housing, seam        |
| `--color-stands`    | `#EDF1EF` | `#E2EAE6` | daylight ground, human side               |
| `--color-agent`     | `#22C8E8` | `#0B7F99` | the agent's side of the glass             |
| `--color-signal`    | `#35E084` | `#0F8A4A` | live / in progress / on air               |
| `--color-brand-blue`| `#0177BE` | `#0166A4` | far end of the ramp — **gradients only**  |
| `--color-brand-lime`| `#9CF216` | `#5F9A0D` | far end of the ramp — **gradients only**  |

The ground is blue-black rather than neutral black, so it sits *under* the brand's cool
end instead of fighting it. Both hues carry `-hot` and `-deep` steps.

`--grad-brand` and `--grad-brand-soft` hold the ramp in one place. Every gradient on the
site is a slice of the logo's own ramp — the headline word, the primary button, the seam,
the arena progress bar. The two `brand-*` tokens exist only to extend that ramp; they are
never used as flat fills, because saturated blue and lime do not hold type.

`.code` derives its syntax scheme from luminance and weight with cyan for endpoint paths:
comment → body → path → verb, faintest to brightest. Code blocks appear on nearly every
page, so the scheme has to stay quiet.

Two roles resolve per ground, so they stay legible on both: `--strong` (full-contrast
text — white on the deck, ink on the stands) and `--signal-fg` (the live hue, stepped
darker on the stands where the bright value fails contrast as small text).

### Dark and light

Both themes ship. The concept does not invert — it re-lights. In light mode the agent's
ground becomes a clinical cool white (an instrument panel under lab light) and the
human's stands step slightly deeper, so the two grounds still separate by **temperature
and a step of value** rather than by one being dark. Both brand hues darken to hold
contrast on light: cyan `#22C8E8 → #0B7F99`, green `#35E084 → #0F8A4A`. `.btn-solid`
also flips to light type, because the light ramp is deep enough that dark type on it
fails contrast.

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

**Background layers**, in back-to-front order:

- `.atmos-aurora` — three brand-coloured lights drifting on 34/44/52-second periods.
- `.atmos-grid` — blueprint rule, masked to fade at the edges.
- `.atmos-beam` — a soft diagonal sweep crossing the ground every 19 seconds. Long
  period on purpose: it should be noticed on second glance, not first.
- `Swarm.astro` — a field of agents that link when they come within range, with the
  occasional packet running down a live link. It is the pool, abstracted: nodes find
  each other, hold a connection, drift apart. Node count scales with area and is capped
  (58 normally, 20 on a low-core device); it pauses offscreen and on hidden tabs, and
  draws a single static frame under reduced motion. Pass `density="low"` and
  `packets={false}` where the section already carries motion.

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

**On palette:** the logo is no longer the odd one out — the whole site's palette is
sampled from it. That was the fix for a colour scheme that read as clashing: the mark is
blue-to-green, so a red accent fought it on every screen. Deriving the two semantic roles
from the two ends of the mark makes the logo look native rather than pasted on.

## The network section

`Network.astro` draws the whole pipeline as four labelled panels, all driven by **one
loop**, so what a panel says is what is genuinely happening in the others at that moment:

| panel | shows |
| --- | --- |
| 01 Data centres | world map, server-rack markers on real cloud regions, agents launching |
| 02 AI agents | a card per agent — id, region, and status (in flight → seated → playing) |
| 03 The game | a live miniature PushBlock match with a goal line and a scoreboard |
| 04 Settlement | a growing block chain plus a ledger of settled pools |

The device that makes it read as one story rather than four unrelated widgets: **an agent
keeps its colour the whole way through** — from its card, to the arc it flies across the
map, to its square on the field, to its row on the scoreboard. Four separable stops along
the brand ramp, one per seat.

The cycle is deploy (agents fly in until four are seated) → match (nine seconds of
simulation) → settle (a block is appended, the ledger gains a row) → repeat.

`src/data/world.ts` holds a 5° equirectangular land mask stored as inclusive column spans
per row — far easier to read and correct than a bitmap string — plus twelve real cloud
regions at real coordinates. Antarctica is omitted, as on most dot maps: a solid bar
across the bottom unbalances the frame and there are no regions on it. The land layer is
rendered once into an offscreen canvas and blitted; only arcs, agents and the hub redraw.

**Every DOM lookup is scoped to the section**, not the document. `Arena.astro` also uses
`[data-clock]`, and a document-wide query wrote this panel's match time into the hero
arena's readout. Scope new components the same way.

**Honesty:** the regions and their coordinates are real; the agents, matches and
settlements are generated in the browser to make the pipeline legible, and the caption
says exactly that. No quantity is invented — same rule the arena follows.

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

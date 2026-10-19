# WDW Dining

Five interactive planning tools for eating and drinking at Walt Disney World.
Static HTML, no build step, no dependencies to install.

| Page | What it does |
|---|---|
| `index.html` | Landing page and router between the five tools |
| `atlas.html` | 134 restaurants plotted on food quality vs. immersion, each with a menu panel |
| `value.html` | 88 quick-service counters scored against the dining plan break-even |
| `quick-service.html` | The same counters with tunable alcohol and plant-based weights |
| `snacks.html` | 112 snacks with a credit allocator and a trap list |
| `drinking.html` | World Showcase drinking passport with standard-drink tracking (21+) |

## Deploying to GitHub Pages

```bash
git init
git add .
git commit -m "WDW dining tools"
git branch -M main
git remote add origin https://github.com/<you>/<repo>.git
git push -u origin main
```

Then in the repository: **Settings → Pages → Build and deployment**, set
*Source* to **Deploy from a branch**, *Branch* to **main** and the folder to
**/ (root)**, and save. The site publishes at
`https://<you>.github.io/<repo>/` within a minute or two.

All internal links are relative, so it works correctly whether it's served
from a project subpath (`/<repo>/`) or a user site at the domain root. It also
works if you just open `index.html` off the filesystem.

## How it's put together

Each page is a single self-contained HTML file: markup, styles, data, and
logic in one place. That is deliberate. There is no bundler, no framework, and
no shared asset directory to break. Editing a page means editing one file.

The only external request is the Google Fonts stylesheet (Archivo, DM Mono,
Public Sans). Remove those two `<link>` tags and the pages fall back to system
fonts and still work offline.

Adding a page means copying the `<nav class="sitenav">` block from any existing
page, adding a matching `<a>` to the nav on every other page, and adding a card
to `index.html`.

### Data

Every page holds its data in one array near the top of its `<script>` block,
with a comment above it explaining the fields:

- `atlas.html` — `const D` — food score, immersion score, price band, status, and an
  optional `menu:[[name, price|null], ...]` array of verified dishes
- `value.html` — `const DATA` — entree ceiling, alcohol tier, plant-based tier
- `quick-service.html` — `const DATA` — base value, alcohol tier, plant-based tier
- `snacks.html` — `const S` — price, eligibility (0 trap / 1 verify / 2 confirmed)
- `drinking.html` — `const ROUTE` — pavilions stored west to east and reversed on
  demand, with rest stops interleaved; each pour carries a price and an
  approximate US standard-drink count

Adding a restaurant, a snack, or a pour means adding one object to the relevant
array. Nothing else needs to change.

### Saved state

Shortlists, snack baskets, and the drinking passport persist via
`window.storage` where it exists, and fall back to `localStorage` otherwise.
Data stays on the visitor's device. Nothing is sent anywhere and there is no
analytics.

Storage keys are namespaced `wdw-dining:` — `atlas-saved`, `snack-basket`,
`qs-planned`, `qs-value-planned`, `epcot-passport`.

## Caveats

Prices are estimates assembled from current reporting and they move. The food
and immersion scores are editorial judgments, not measurements. Dining plan
credit eligibility varies by location and changes without notice; items marked
*check the board* are flagged for that reason.

Standard-drink counts in the passport are approximations, since World Showcase
pour sizes are not published and vary considerably. They exist to keep the
running total visible, not to substitute for paying attention.

Menus are deep-linked to Disney's official pages rather than copied, because
they rotate constantly. The optional `menu` array on an atlas entry lists only
dishes that were actually verified; entries without one render an explicit
"no list on file" state. To add dishes to a restaurant, append a `menu` array
to its object.

Everything reflects July 2026.

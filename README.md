# WDW Dining

Four interactive planning tools for eating at Walt Disney World. Static HTML,
no build step, no dependencies to install.

| Page | What it does |
|---|---|
| `index.html` | Landing page and router between the four tools |
| `atlas.html` | 134 restaurants plotted on food quality vs. immersion |
| `value.html` | 88 quick-service counters scored against the dining plan break-even |
| `quick-service.html` | The same counters with tunable alcohol and plant-based weights |
| `snacks.html` | 112 snacks with a credit allocator and a trap list |

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

### Data

Every page holds its data in one array near the top of its `<script>` block,
with a comment above it explaining the fields:

- `atlas.html` — `const D` — food score, immersion score, price band, status
- `value.html` — `const DATA` — entree ceiling, alcohol tier, plant-based tier
- `quick-service.html` — `const DATA` — base value, alcohol tier, plant-based tier
- `snacks.html` — `const S` — price, eligibility (0 trap / 1 verify / 2 confirmed)

Adding a restaurant or a snack means adding one object to the relevant array.
Nothing else needs to change.

### Saved state

Shortlists and snack baskets persist via `window.storage` where it exists, and
fall back to `localStorage` otherwise. Data stays on the visitor's device.
Nothing is sent anywhere and there is no analytics.

Storage keys are namespaced `wdw-dining:` — `atlas-saved`, `snack-basket`,
`qs-planned`, `qs-value-planned`.

## Caveats

Prices are estimates assembled from current reporting and they move. The food
and immersion scores are editorial judgments, not measurements. Dining plan
credit eligibility varies by location and changes without notice; items marked
*check the board* are flagged for that reason. Everything reflects July 2026.

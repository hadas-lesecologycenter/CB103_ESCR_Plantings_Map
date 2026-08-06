# Secondhand LES & East Village — setup & upkeep

The map lives at **`docs/thrift-map.html`**. It is one self-contained file, like the
tree maps in this repo, and it works on GitHub Pages with no build step.

It reads its shop list from the first source that answers:

1. **Your Google Sheet** (once you set `SHEET_URL`) — the intended day-to-day source
2. **`docs/shops.csv`** — the starter list committed here
3. **A copy baked into the HTML** — so the map never renders empty

The badge in the top-right corner always tells you which one is live.

---

## 1. Set up the Google Sheet

1. Create a new Google Sheet. Name the first tab exactly **`Shops`** (capital S — the
   map asks for that tab by name).
2. **File → Import → Upload**, choose `docs/shops.csv`, and pick **Replace current sheet**.
   You now have 36 shops and the correct 16 columns.
3. **File → Share → Publish to web**. Choose the **Shops** tab and **Comma-separated
   values (.csv)**, then **Publish**. Copy the link it gives you — it looks like:

   ```
   https://docs.google.com/spreadsheets/d/e/2PACX-1vT.../pub?gid=0&single=true&output=csv
   ```

4. Open `docs/thrift-map.html`, find this near the top of the `<script>` block, and paste
   the link in:

   ```js
   var SHEET_URL = 'https://docs.google.com/spreadsheets/d/e/2PACX-.../pub?gid=0&single=true&output=csv';
   ```

5. Commit and push. From then on, editing the sheet changes the map on the next page
   reload — no code, no deploy.

> **Note:** "Publish to web" makes that tab publicly readable. Keep anything private in a
> different sheet, not a different tab.

---

## 2. Set up the submission Form

1. Create a Google Form called something like **"Add or fix a shop"**. Suggested questions
   — keep the wording, it makes copying answers across trivial:

   | Question | Type |
   |---|---|
   | Shop name | Short answer |
   | Street address | Short answer |
   | Is this a new shop, a correction, or a closure? | Multiple choice |
   | Kind of shop | Multiple choice — the seven values in §4 |
   | Price | Multiple choice — $ / $$ / $$$ |
   | What do they sell? | Checkboxes — the nine values in §4 |
   | What eras? | Checkboxes — the seven values in §4 |
   | Nonprofit or mission-driven? | Yes / No |
   | Do they buy, take consignment, or trade? | Checkboxes |
   | Hours | Short answer |
   | Website or Instagram | Short answer |
   | Anything a shopper should know? | Paragraph |
   | Did you visit in person? | Yes / No |

2. In the Form's **Responses** tab, click the Sheets icon and **link it to the same
   spreadsheet**. Google adds a `Form Responses 1` tab beside `Shops`.
3. Copy the Form's public link into `thrift-map.html`:

   ```js
   var FORM_URL = 'https://forms.gle/xxxxxxxx';
   ```

   That switches on the **+ Add a shop** button and the "tell us what you found" link
   inside every unverified popup.

**Submissions do not go straight onto the map, by design.** They land in
`Form Responses 1`; someone reviews them and copies good rows into `Shops`. A public map
with a public write path gets spammed, and a wrong address sends people across the
neighborhood for nothing. Reviewing is a two-minute job.

---

## 3. Verifying the starter list

Every one of the 36 rows currently has `verified` = `no`, and every popup says so. That
flag is the point of the list, not a defect in it — it is a worklist.

To verify a shop: confirm it is open, get the real hours, get the real Instagram or
website, then set `verified` to `yes` in the sheet. The unverified badge disappears and
the counter in the sidebar drops.

Three rows carry a note in the `notes` column flagging something specific I could not
confirm — check those first:

- **Marmalade Vintage** — address needs confirming
- **INA NY** (21 Prince St) — confirm this location is still trading
- **White Trash NYC** — confirm still open
- **Trash and Vaudeville** — stocks mostly new reproduction rather than true vintage;
  delete the row if you want the map to be strictly secondhand
- **Strand Book Store** — at 12th & Broadway, just outside the neighborhood line; delete
  it if you want a tighter boundary

`hours` is blank for every shop and `link` is filled only where I was confident. Blank
fields simply don't render in the popup, so partial data looks fine — fill them as you go.

### Fixing pin positions

Coordinates were derived from the Manhattan street grid, so pins land on the right block
but not necessarily the right doorway. To correct them:

1. Open the map with **`?admin=1`** on the end of the URL.
2. Drag any pin onto its real entrance.
3. Click **Copy all coordinates** in the panel that appears, and paste the `lat` and `lng`
   columns back into the sheet.

Visitors never see this mode.

---

## 4. Column reference

| Column | What goes in it |
|---|---|
| `name` | Shop name. Also how walking routes find their stops — rename carefully. |
| `address` | Street address, no city or state. |
| `neighborhood` | Free text, shown after the address. |
| `lat`, `lng` | Decimal degrees. A row with either missing is hidden and counted in the badge. |
| `type` | Exactly one of: `Vintage boutique`, `Thrift & charity`, `Consignment`, `Archive & designer`, `Books & records`, `Home & furniture`, `Flea & market`. Anything else falls back to Vintage boutique. |
| `price` | `1`, `2`, or `3` — renders as $ / $$ / $$$. |
| `goods` | Any of `Clothing`, `Accessories`, `Jewelry`, `Eyewear`, `Housewares`, `Furniture`, `Books`, `Records`, `Art`, joined by `\|`. |
| `eras` | Any of `Pre-1960s`, `1960s-70s`, `1970s-80s`, `1980s-90s`, `1990s-Y2K`, `Y2K-2010s`, `Mixed`, joined by `\|`. |
| `nonprofit` | `yes` / `no`. Drives the mission-driven filter. |
| `buysell` | Any of `Buys from public`, `Consignment`, `Trade credit`, `Sells only`, joined by `\|`. Anything but `Sells only` matches the "places that take your stuff" filter. |
| `hours` | Free text, e.g. `Wed–Sun 12–7`. Hidden when blank. |
| `link` | Full URL including `https://`. Hidden when blank. |
| `blurb` | One or two sentences on what you'll actually find. |
| `verified` | `yes` / `no`. |
| `notes` | Internal only — never shown on the map. |

Multi-value columns use a pipe (`|`), not a comma, so they survive CSV export.

---

## 5. Editing the walking routes

Routes are defined in `thrift-map.html` in the `ROUTES` array, not in the sheet — they
change rarely and want a human-written description:

```js
{ name:'East 7th to 11th ramble', color:'#A8442A',
  desc:'The densest run of vintage in the East Village — about 25 minutes of walking…',
  stops:['Metropolis Vintage','Search & Destroy', /* … in walking order … */] }
```

`stops` are shop **names**, matched against the sheet. A name that doesn't match is
silently skipped, so if a stop vanishes from a route, check for a typo or a renamed shop.

The route line is deliberately **dashed and drawn straight between stops** — it shows the
order to walk them in, not turn-by-turn directions. Nobody needs turn-by-turn on a grid.

---

## 6. Known gaps

- **Home, furniture and housewares coverage is thin** (three shops). That reflects the
  neighborhood — most of the vintage furniture trade left for Brooklyn — but it's the
  category most likely to be missing something I don't know about.
- **Large charity thrift is nearly absent.** Housing Works' bookstore is here; its
  clothing branches and the surviving Goodwill and Salvation Army stores are all outside
  the boundary. Worth deciding whether to stretch the line for them.
- **The Ecology Center's own reuse programming isn't on the map.** Swaps, the e-waste
  drop-off, and any Stuff Exchange events would fit naturally as their own `type`, and
  would be the one thing here nobody else could publish.

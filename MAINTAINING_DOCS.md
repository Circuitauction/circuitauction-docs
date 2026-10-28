# Maintaining These Docs (screenshots + text)

Maintainer runbook for regenerating the screenshots and keeping the help-page text in
sync with the app. Everything below was worked out and verified in August 2026, when all
screenshots were recaptured and the text was audited against the current UI.

Not published in the sidebar — this file is for whoever updates the docs next
(human or AI agent).

---

## 1. Environment

| What | Where |
|------|-------|
| Backoffice client (screenshots are taken here) | `https://backoffice.ddev.site:9010` (`ddev grunt serve` in `circuitauction-backoffice/client`) |
| Backoffice Drupal backend | `https://backoffice.ddev.site` |
| Live app (clerk / auctioneer / room / bidder) | `https://backoffice.ddev.site/live/#/...` — served by the Drupal site, **not** by :9010 |
| Local bid server (separate ddev project) | `https://circuit-bid.ddev.site:4443`, repo `/media/bl/Disk2/htdocs/circuit-bid` |

**Always use the `demo` database** — it contains only fake clients (James Smith,
William Brown, donald duck…), so screenshots are safe for a public site. The active DB is
the un-commented `'database'` line in
`circuitauction-backoffice/server/www/sites/default/settings.ddev.php`.

### Demo fixtures used by the current screenshots

- **Sale 792 "Stamps"** — sale number 17, uuid `test-698cf00431c881-82833186`, 23 items,
  published on site. This is the sale-context fixture for almost every shot.
- Other sales: 2237 "Le Duc" (243 items), 858 "Memorabilia", 38 "Stamps of the World #108" (finished).
- **Client 12 "William Brown - 100005"** — the client-page fixture. Has bidder number
  **#2001** (website) with limits, floor numbers #1/#2, phone bidder **#300**
  (created by a call request for lots "14, 15-16"), and a $400 book bid on lot 12.
- **Consignment 827 "Consigner William Brown"** — commission steps (15% from 0, 10% from
  20000), finder fee, additional charge, and 9 tasks (incl. Under Extension tasks).
- Client statuses in demo: 25 approved, 10 new, 3 unapproved; tag `Passive` on one client
  (used for the tag-filter shot).

### Item moves fixtures (version 3.6, seeded 2026-10-28)

Seeded by `circuitauction-backoffice/.superpowers/sdd/2026-10-26-sibling-move-item-history/seed-demo.php`
(a one-off `drush scr`; the chain is built with the real clone code, the sibling hop is written
by hand). Used by the `item-moves` tutorials and the Items > Clone or Move help page.

- **Sale 38 "Stamps of the World #108"** (sale number 1, finished), consignment **254**
  "Consigner William Brown": where the lots start.
- **Sale 3691 "Autumn Stamps Sale"** (renamed from "1", sale number 18, status new, no lot
  numbers): the return sale and the destination of the clone-rules tutorial.
- **Lot 3 of sale 1 = item 262** → cloned into sale 17 "Stamps" as item **3800** (status
  `moved_to_remote_bo`, external history row to the sibling) → back from the sibling into
  sale 18 as item **3806** (its first history row carries the sibling origin, no local
  connected record). Chain: Sale 1 → Sale 17 → Demo US → Sale 18.
- **Lot 4 of sale 1 = item 265** → cloned into sale 17 as item **3809** only. The clone-rules
  tutorial force-clones 265 into sale 18 on every run and leaves the clone there (delete it
  afterwards if the fixture must stay canonical).
- **Sibling backoffice "Demo US"**: term 318 of `connected_backoffices`, domains
  `demo-us.circuitauction.test`, **no secret**, so nothing is ever fetched from it and the
  chain ends at its box. `server_general_backoffice_name` = "Demo Europe".
- `backoffice_item_display_column` has `moved_to` right after `lot` (the "Moved to" column, visible
  without horizontal scrolling in the shots and the videos).

Screenshots: `item-chain`, `item-history-external`, `family-tree-chain`, `items-list-moved-to`,
`clone-move-force-clone` (all in `assets/screenshots/`, captured with the
Cypress spec `client/cypress/docs-shots/item-moves.cy.js`:
`cd client && npx cypress run --browser chrome --config-file cypress.docs.config.js` (Electron's
`cy.screenshot()` hangs here) at 1680×950, 1920×950 for the history tab, then copied from
`client/cypress/screenshots/`; a refused lot is
answered at once as an alert in the form, nothing is queued, so the refusal and the ticked
"Force clone items" are one shot).

## 2. Authentication for headless captures

Get a token and seed it **before the app boots** (the login form is not needed):

```bash
curl -k -s https://backoffice.ddev.site/admin/get-test-api-token   # -> {"token": "..."}
```

Playwright init script:
`localStorage.setItem('bo_access_token', TOKEN); localStorage.setItem('login_uid', '1');`

**Gotcha:** the token endpoint's response is served from the anonymous *page cache*. If
the cached token's `restful_token_auth` row has been purged you will get
`401 Bad credentials` on every API call and the app won't boot. Fix:

```bash
ddev drush php-eval "cache_clear_all('*','cache_page',TRUE); cache_clear_all('test_api_token','cache');"
```

## 3. Connecting the local bid server (needed for Bids / Bidding info / live app)

The bid server ddev project is normally already running (`ddev describe` in
`circuit-bid`). Site **backoffice-demo**, id **49548** is registered on it. The BO's
`circuit_bid_server_host` variable already points at it.

The BO authenticates by POSTing the **current BO user's email** to the bid server
(`api/bo_auth`), which must match a bid-server user of that site:

```bash
# 1. Point uid 1 at the bid-server user (TEMPORARY — restore when done!)
ddev drush sql-query "UPDATE users SET mail='admin@backoffice-demo.com.test' WHERE uid=1"
# (uid 1 already has the required 'clerk' role)

# 2. In the client UI: click the plug icon (top right) -> "Refresh connection"
#    The plug turns green. Sale pages / Bidding info / Bids pages now work.

# 3. When completely done:
ddev drush sql-query "UPDATE users SET mail='brice@circuitauction.com.test' WHERE uid=1"
```

### Syncing a sale to the bid server

On the sale form: **Resync with Bid server** → confirm "Yes" (Angular modal), then also
**Sync Sale info with Bid Server**. The items sync via advancedqueue — there is no queue
runner locally, so run it twice (first run fans out per-item tasks):

```bash
ddev drush advancedqueue --all --timeout=200
ddev drush advancedqueue --all --timeout=200
```

The sale form's "Bid Server Sale Status" box then shows an auction status
(e.g. *Closed - not started*) instead of "Sale is not synced with bid server".

## 4. The live app (clerk / auctioneer / room / bidder)

URLs (note the bidder page has **no** `/en` suffix — with `/en` you get *Access denied*):

```
/live/#/sale/<uuid>/clerk        (staff)
/live/#/sale/<uuid>/auctioneer   (staff)
/live/#/sale/<uuid>/room/en      (public)
/live/#/sale/<uuid>              (bidder page, log in as a bidder)
```

Logins used for the screenshots (passwords were set on the **bid server**):

```bash
cd /media/bl/Disk2/htdocs/circuit-bid
ddev drush upwd 'admin@backoffice-demo.com~49548' --password='Demo1234!'   # clerk
ddev drush upwd 'james-smith@example.com~49548'  --password='Demo1234!'   # bidder
```

Log in with `admin@backoffice-demo.com` / `james-smith@example.com` + that password.
(The live login is federated through the BO, so the clerk login only works while uid 1's
mail is swapped as in section 3.)

**Big gotcha:** logging into the live app as the clerk **rotates that user's bid-server
token and silently breaks the BO's connection** ("Bad credentials" inside Bidding info).
Do all live-app captures in one block, then re-do plug → Refresh connection for the BO.

### Driving the clerk (v2 Elm app)

- Sale status: click the status pill (top left) → pick a status → click the **arrow
  (`button.ss-apply`)** next to the pill. Picking alone does nothing. Menu offers only
  *Mail auction open / Starting soon / Live auction open / Paused - back shortly*;
  you cannot go back to *Closed - not started* from the UI — reset it in the bid-server DB:
  `UPDATE field_data_field_sale_status SET field_sale_status_value='closed_not_started' WHERE entity_id=<nid>` (+ same in `field_revision_...`, then `node_save`).
- Item states: **STAND BY / OPEN / GOING / GONE** buttons (disabled until the sale is live).
  GONE auto-advances to the next lot — for "Gone" screenshots the sold confirmation shows
  in the messages feed / CLOSED panel / room footer.
- **PLACE BID** places a floor bid at the next step; **ENTER AMOUNT** for custom amounts;
  **SET TO FLOOR**; **STEP**. Realtime flows over Pusher (cloud) and works locally.
- After opening any dropdown a `.menu-backdrop` div may keep intercepting clicks —
  remove it via JS before the next click.
- Book bids in the BO ("Enter bids") fail with *"Sale has not started yet"* until the
  auction status is **Mail auction open** (or live).

## 5. Screenshot conventions

- All app screenshots live in **`assets/screenshots/`** with descriptive kebab-case names
  (`clients-table-sort.png`, `auction-clerk-going.png`, `bidder-live-credit.png`, …).
  The legacy `.gitbook/assets/image (N).png` files are only referenced by a few
  not-yet-recaptured images (see section 7).
- Viewports used: 1440×900 (login/dashboard/live app), 1680–1695×950 (tables and forms).
  Device scale 1. Clip to the relevant block with ~8–16px padding rather than shipping
  full-page shots.
- The auction-flow matrix files are `auction-{clerk|auctioneer|room|web}-{paused|active|going|gone}.png`
  (file names kept the old "paused/active" wording; the UI labels are Stand by/Open).

### Capture harness (Playwright, Python)

The harness used for every capture (recreate as `cap.py`, run scripts with `python3`):

```python
import json, subprocess, time
from playwright.sync_api import sync_playwright

BASE = "https://backoffice.ddev.site:9010"
OUT = "/media/bl/Disk2/htdocs/circuitauction-docs/assets/screenshots"

def get_token():
    out = subprocess.check_output(
        ["curl", "-k", "-s", "https://backoffice.ddev.site/admin/get-test-api-token"])
    return json.loads(out)["token"]

class Cap:
    def __init__(self, headless=True, viewport=(1440, 900), authed=True):
        self.pw = sync_playwright().start()
        self.browser = self.pw.chromium.launch(headless=headless)
        self.ctx = self.browser.new_context(
            ignore_https_errors=True,
            viewport={"width": viewport[0], "height": viewport[1]})
        if authed:
            token = get_token()
            self.ctx.add_init_script(
                f"localStorage.setItem('bo_access_token', '{token}');"
                "localStorage.setItem('login_uid', '1');")
        self.page = self.ctx.new_page()

    def boot(self, route="/dashboard/dashboard", wait=6):
        self.page.goto(BASE + "/#" + route)
        self.page.wait_for_load_state("networkidle", timeout=30000)
        time.sleep(wait)

    def nav(self, route, wait=4):
        # page.goto() with a new hash often bounces to the dashboard; set the hash instead
        self.page.evaluate("h => { location.hash = h; }", route)
        time.sleep(wait)

    def set_sale_context(self, query, choice_text=None, wait=4):
        self.page.evaluate("() => window.scrollTo(0,0)")
        self.page.locator(".ui-select-container").first.click()
        time.sleep(1)
        self.page.locator("input.ui-select-search").first.fill(query)
        time.sleep(3)
        self.page.locator(".ui-select-choices-row", has_text=choice_text or query).first.click()
        time.sleep(wait)

    def shot(self, name, selector=None, pad=12):
        path = f"{OUT}/{name}.png"
        if selector:
            box = self.page.locator(selector).first.bounding_box()
            self.page.screenshot(path=path, clip={
                "x": max(0, box["x"] - pad), "y": max(0, box["y"] - pad),
                "width": box["width"] + 2 * pad, "height": box["height"] + 2 * pad})
        else:
            self.page.screenshot(path=path)

    def close(self):
        self.browser.close(); self.pw.stop()
```

Hard-won selector notes:

- **Tab content on entity pages does not render from the URL alone** — click the exact
  tab link first: `[...document.querySelectorAll('.nav-tabs a')].find(a => a.innerText.trim() === 'Sale').click()`
  (sale forms are slow — wait ~12s after the click).
- Sale/client **status pill** = `button[id^='statusMenu']`; the open menu items are
  `.label-status` divs.
- Sale-context and tag filters are **ui-select** widgets: click `.ui-select-container`,
  type into `input.ui-select-search`, click a `.ui-select-choices-row`. The client-tags
  filter has `id="client-tags"`.
- DataTables: wait for `table tbody tr td`; header filter inputs are
  `table thead input.text_filter` / `select.select_filter`.
- The plug icon is `bid-server-access li.dropdown` (the custom element itself has no box).
- The Elm clerk app: state buttons by visible text, apply = `button.ss-apply`.

## 6. Current terminology cheat-sheet (for text audits)

Verified against the code/UI in Aug 2026 — if a help page contradicts this, the page is stale:

| Area | Current truth |
|------|---------------|
| Sale statuses | New, Ready to publish, Published on site, Finished (no more "In Process" / "Post sale purchase") |
| Client statuses | New, Pending, **Bid Approved**, **Bid Unapproved**, **Inactive** |
| Credit requests | Renamed **Bidding Limit requests** (side menu + client page block) |
| Bidder number types | Floor, Floor by agent, Website, Online, Phone, External — created via **Register Bidder**; limits are **Bidding Limit** and **Personal Limit** (None/Unlimited/Regular + amount) |
| Client page tabs | Client, Addresses, Bidding info, History, Viewings, Consignments, Items consigned, Items bought, Bidder invoices, Transactions, Statement of Account, Activities, Emails and Documents, Tasks |
| Bidding report | Via **Emails and Documents** tab → Send email → e.g. `Bids Statement` template (the old "Reports" tab is gone) |
| Find X in a table | No more green-expand / eye icons — the ID / title link opens the record |
| Create an item | From the consignment page (**Add new item to consignment** / **Quick Add Item**); no "+create new item" on the Items dashboard |
| Bulk assign items to a sale | **Clone/ Move Item** panel on the Items dashboard |
| Lot numbering | Sort by buttons, **New lot #** + **Apply**, **Save**, **Lock lot #** toggle; route `#/dashboard/sales/<nid>/info/assign-lot-numbers` |
| Tasks | A **Tasks tab** on entity pages; fields Type/Assignee/Status/Due date + **Created** button; statuses Can start / Can't start / Completed; per-row Save/Delete |
| Live item states | Stand by, Open, Going, Gone |
| Auction statuses (clerk menu) | Mail auction open, Starting soon, Live auction open, Paused - back shortly (+ closed states shown but not selectable); custom message via **Update Paused Message** for Starting soon / Paused |
| Item images sync | "Items images sync" block on the sale form shows the S3 directory (e.g. `Sales/17`) and progress |
| Phone bidder cards | Per-row **PDF** button + **Download session phone bidders card** dropdown |

## 7. Images intentionally NOT recaptured

- `sale/how-to-mass-upload-items-images.md` — two AWS S3 console screenshots (external tool).
- `client/README.md` — the "right to place a bid per status" table (a Google-Docs table, not UI).
- `client/how-to-download-phone-bidder-cards-list.md` — two Chrome PDF-viewer shots.
- `items/under-extension-worflow.md` — auctioneer UE badge, statement-with-UE-item and
  Expert Orders tab (need expertising data that the demo DB lacks).
- `consignment/consignment-workflow.md` + `how-to-find-related-consignment-statement.md` —
  old `.gitbook` images of the workflow indicator / related-statements popup.
- `_coverpage.md` references `.gitbook/assets/logo.png` which **does not exist** (pre-existing).
- `website/*`, `data-migration/*`, `it-section/*` — WordPress site / developer docs, out of scope.

## 8. Cleanup checklist after a capture session

1. Restore uid 1's email in the demo DB (section 3) — this also returns the plug icon to
   its usual red/disconnected state.
2. If you opened the auction, reset the bid-server sale status to `closed_not_started`
   (section 4) — the UI cannot go backwards.
3. Demo data created by captures (bidder numbers, test bids, call requests) is harmless
   and can stay; it makes future screenshots richer.
4. Delete any `_probe-*.png` files from `assets/screenshots/` and remove screenshots no
   longer referenced by any page.
5. Verify every reference resolves — quick check:
   every `![...](...)` path in the `*.md` files must exist on disk, and any remaining
   `user-images.githubusercontent.com` / `circuitdemo.s3` URLs should be on the
   intentional list in section 7.

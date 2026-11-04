---
description: Bulk import handlers for items, clients, consignments, bids and related data.
---

# Data Migration

The back-office ships with a set of migration handlers used to import data in bulk from a Google Sheet (CSV) or, in some cases, a remote API. Each handler is responsible for a specific entity (items, clients, addresses, bids, etc.) and exposes its own set of source columns.

All handlers share the same general workflow:

1. Prepare a source file (usually a Google Sheet) with the expected column headers.
2. Paste the **CSV export URL** of the sheet in the corresponding form on the back-office.
3. Trigger the queue to process the rows.
4. Review the results table and re-queue failed rows if needed.

{% hint style="info" %}
For Google Sheets the URL must be the CSV export form, not the regular `edit` link. The pattern is:

```
https://docs.google.com/spreadsheets/d/<SPREADSHEET_ID>/export?format=csv&gid=<SHEET_GID>
```
{% endhint %}

## Available Handlers

### Items

| Handler | Purpose |
|---|---|
| [Items (Self-Service)](items.md) | Import new items in bulk, auto-create consignment and consignor when needed. |
| [Items Update](items-update.md) | Update existing items by `nid`, `_internal_id` or by lot. Will not create new items. |
| [Items Sold Result](items-sold-result.md) | Import sold prices and winning bidders, creates item history records. |
| [Item Catalogs](items-catalogs.md) | Attach catalog references (multifield) to existing items. |
| [Item Dimensions](items-dimensions.md) | Attach dimension / package info (multifield) to existing items. |

### Clients & Addresses

| Handler | Purpose |
|---|---|
| [Clients](clients.md) | Import client nodes (buyers & consignors). |
| [Clients Update](clients-update.md) | Update existing clients matched by email. |
| [Clients Password](clients-password.md) | Migrate password hashes onto existing clients. |
| [Addresses](addresses.md) | Import and attach address nodes to existing clients. |
| [External IDs](external-ids.md) | Attach external system IDs (multifield) to clients. |
| [Clients Bank Accounts](clients-bank-accounts.md) | Attach bank account details (multifield) to existing clients. |

### Categories, Sales & Consignments

| Handler | Purpose |
|---|---|
| [Categories](categories.md) | Import the `categories` taxonomy with hierarchy and translations. |
| [Sales](sales.md) | Import sale nodes. |
| [Consignments](consignments.md) | Import consignment nodes (incl. commission step type and default commission). |
| [Consignment Commission Steps](consignment-commission-steps.md) | Attach commission step multifields to consignments. |
| [Consignment Finder Fees](consignment-finder-fees.md) | Attach finder fee multifields to consignments. |
| [Transactions](transactions.md) | Import transaction nodes for orders. |

### Bids

| Handler | Purpose |
|---|---|
| [Bids](bids.md) | Generic CSV/SQL bid import. |
| [Philasearch Bids](bids-philasearch.md) | Fetch and import bids from the Philasearch API. |
| [Delcampe Bids](bids-delcampe.md) | Import bids from a Delcampe export. |
| [Auction Mobility Bids](bids-mobility.md) | Import bids exported by Auction Mobility. |
| [SAN Bids](bids-san.md) | Import bids from a SAN export. |

## Import Order & Dependencies

The Drive (CSV) handlers drop the inter-migration dependencies, so **you control the run order**. The recommended order is:

```
categories
  → clients
    → addresses · bank accounts · external ids · bidder numbers · client commission steps
  → sales
  → consignments  (+ consignment commission steps · finder fees)
  → items
  → orders
  → buyer transactions
  → consignor statements / payouts
  → images (the `image_url` column of the items sheet, see [Items](items.md#images))
```

Key points:

* **Consignments do not have to precede items.** The [Items (Self-Service)](items.md) handler auto-creates the consignment and consignor from the item row when `_consignment` holds a consignor email / customer id / consignment id before the first pipe. Importing consignments first is still recommended when you have rich seller data, because it gives you control over commission steps, tax type and consignment ids up front.
* **Orders auto-create from sold results** (one order per sale + buyer), or can be imported explicitly.
* A consignment **statement** is auto-created from the item / sold-result flow — there is no separate statement import for the auto-derived lines.

## Keys & Matching

The migration is anchored on stable source IDs so relationships survive re-runs:

* **Clients** are keyed by `_customer_id` → `field_philaworksplace_id`. This is the resolver used by almost every client-side handler (addresses, bank accounts, external ids, item → consignor, etc.), so client emails do **not** need to be globally unique. The one exception is the **sold-result winner lookup**, which currently resolves the winning bidder by email only.
* **Items** are keyed by `_internal_id` → `field_internal_id`, re-found by sold-results and transactions. When no sale is supplied the first match wins, so `_internal_id` should be globally unique.
* **Sales** are matched by `_sale_number` (the human auction ref). It is a free-form text field but must be unique — first match wins.
* **Categories** are matched by `_hk_id` → `field_hk_cat_id`.
* **Same physical artwork across several sales:** model each appearance as its own item and put the shared artwork id in `_search_tag` (`field_search_tags`) to group all appearances. There is no first-class "parent artwork" entity.

## Common Conventions

* **Unique identifier:** Each row must carry a `_unique_id` (or equivalent) column. The migration map uses it to track which rows have already been imported and to avoid duplicates.
* **Unknown columns are ignored.** Drive handlers read the header row and skip any source column that has no field mapping, so extra (e.g. `gap_*`) columns are inert — useful for carrying data that has no Circuit home yet for later review.
* **Languages:** handlers read `_<field>_<langcode>` columns and only populate a second language when the corresponding `*_second_language` variable is set. English-only data (populate `_..._en`, leave others blank) is fully supported.
* **Formats:** deliver dates as ISO `YYYY-MM-DD` (parsed via `strtotime()`), amounts as plain numbers with no currency symbol, and files as UTF-8. Price columns strip a leading currency symbol automatically, but avoid thousands separators on non-price amount columns — not every handler strips commas.
* **Skipping a row:** When a row references a missing entity (client, item, consignment) the handler logs the reason and skips the row — the **Info** column of the results table shows why.
* **Re-running an import:**
  * **Queue import items** only processes rows that have never been imported.
  * **Queue failed import items** retries only the rows that previously failed.
  * **Queue update All items data** re-imports every row and **overwrites manual back-office changes** — use with caution.
* **Test mode:** In non-live environments, `.test` is appended to outgoing emails to prevent contacting real clients.

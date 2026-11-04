---
description: Bulk import of items from a Google Sheet (ServerItemsSelfServiceMigrate).
---

# Items (Self-Service)

Handler: `ServerItemsSelfServiceMigrate` — extends `ServerItemsDriveMigrate`.

This is the main item importer. It creates new item nodes from a CSV / Google Sheet and can auto-create the consignment and the consignor when they don't already exist. It can also download the item images from the URLs of an `image_url` column (see [Images](#images)).

**Example Sheet:** [https://docs.google.com/spreadsheets/d/13OhdvytrpmdOLLAUoC2mRi7jTs6lzKCccyNDqus8lgo/edit#gid=219859970](https://docs.google.com/spreadsheets/d/13OhdvytrpmdOLLAUoC2mRi7jTs6lzKCccyNDqus8lgo/edit#gid=219859970)

## Step-by-Step Instructions

![Import items flow](../assets/import-items-flow.png)

1. To create the consignment automatically while importing, the `_consignment` field must include the 3 parameters in this format:

   ```
   <email>|<consignment number>|<consignment name>
   ```

2. The easiest workflow is to work with a Google Sheet, so you can make changes and reimport if any row fails due to missing data.

   Paste the URL of the sheet in the **Google drive file URL** textbox (see **1**). You can paste the regular `edit` link straight from the browser — the Backoffice converts it to the CSV export form automatically:

   ```
   https://docs.google.com/spreadsheets/d/<SPREADSHEET_ID>/export?format=csv&gid=<SHEET_GID>
   ```

   The corresponding edit URL (origin) looks like:

   ```
   https://docs.google.com/spreadsheets/d/<SPREADSHEET_ID>/edit?gid=<SHEET_GID>#gid=<SHEET_GID>
   ```

   An **Open sheet** link appears next to the textbox once a valid sheet URL is entered, taking you back to the editable Google Sheet.

   {% hint style="info" %}
   The **Item Import** page has tabs for the related per-sale imports that share this flow: **Items** (this page), [Catalogs](items-catalogs.md), [Dimensions](items-dimensions.md) and [Sold Results](items-sold-result.md). Each tab has its own URL / file input, queue buttons and results table, plus a **Help** link to its page in this documentation.
   {% endhint %}

3. Click **Queue import items** (see **2**) to start the import.

   {% hint style="info" %}
   This function will only attempt to import items that were never imported before. It is safe to use for the first import on the document, or to add new items without overriding items that have already been imported.
   {% endhint %}

4. After the import is done, click **Load Results** (see **5**) to display the import result table.

5. If some items could not be imported, the reason will appear in the **Info** column (see **6**). Images that could not be downloaded are listed there too (row number + URL); the item itself is still imported.

6. For failed imports, click **Queue failed import items** (see **3**) to retry only the rows that failed.

7. To reimport everything and overwrite previous items, click **Queue update All items data (will overwrite changes)** (see **4**).

   {% hint style="warning" %}
   This option will override any change made manually on items in the back-office. Use it carefully. Images are only replaced when the row's `image_url` cell changed, see [Re-import](#re-import).
   {% endhint %}

## Required Fields

* **`_unique_id`** - Unique identifier for each row (required for tracking). A row without one is skipped.

## Consignment & Sale Fields

* **`_consignment`** - Supports multiple formats:
  * `<seller email>|<consignment ID>|<consignment name>`
  * `<consignment ID>` (a single value is first tried as a consignment id of the sale; when nothing matches it is read as a consignor, as below)
  * `<customer ID>` (numeric)
  * `nid:<node_id>` (consignor node ID)
  * `<email address>` (consignor email)

  The consignment id is stored as a number. A lettered id (a receipt number such as `R20003136`) is looked up and stored by its digits; the lettered form is kept in the title of a consignment the import creates (`R20003136 - <name>`).

{% hint style="info" %}
Resolution order, for each row:
1. Consignment by **consignment id** in the sale (second segment, or a bare single value)
2. Consignment by **consignment name** in the sale (third segment)
3. Consignor by the first segment: **customer id**, **`nid:`** or **email**; a consignor that is not found is auto-created (requires `consignor_last_name` or `consignor_full_name`; a numeric first segment becomes the new client's customer id)
4. The consignor's existing consignment in the sale, otherwise a new consignment is created for the sale and consignor (consignment id and name from the row when given)
{% endhint %}

A consignment the import creates gets:

* its commission steps from the row's `_commission` column; otherwise copied from the consignment with the same consignment id in a previous sale (step type included); otherwise the site's **Default Commissions Steps** (Server Settings) with the "single lots" step type;
* the site's default tax type (**Default tax type term ID**, falling back to "automatic").

* **`_sale`** - Sale node ID. On the **Item Import** page it is always the page's sale (injected from the form's `sale_nid` argument); a `_sale` column in the sheet is ignored there.
* **`_sale_number`** - Sale number, only used when the handler runs without a sale context (not from the Item Import page).

## Lot Information

* **`_lot_number`** - Lot number. A value with a letter (`12A`) is split into lot number `12` and lot letter `A`.
* **`_lot_letter`** - Lot letter
* **`_temp_lot_number`** - Temporary lot number
* **`_internal_id`** - Internal item ID
* **`_position_of_consignment`** - Position within the consignment

## Content Fields (Multi-language Support)

Replace `{language}` with language code (e.g., `en`, `he`):

* **`_title_{language}`** - Item title. A title longer than 255 characters cannot be stored as the title: the full text is prepended to the body (as a paragraph) and the title is cleared.
* **`_item_subtitle_{language}`** - Item subtitle
* **`_body_{language}`** - Item description (HTML supported, will be cleaned)
* **`_footer_{language}`** - Footer text (HTML supported, will be cleaned)
* **`_provenance_{language}`** - Provenance information (HTML supported, will be cleaned)
* **`_youtube_video`** - A YouTube video, embedded at the end of the body (site language). Accepts the bare video id (with or without a leading `/`), a `youtu.be/` link, a `watch?v=` link or an `/embed/` link.

Example: `_title_en`, `_title_he`, `_body_en`, `_body_he`

The second language is the one configured in the `migrate_items_csv_second_language` variable; its title, subtitle and body columns are written as translations.

## Images

* **`image_url`** (or **`_image_url`**) - One or more image URLs separated by `|`, in display order:

  ```
  https://cdn.example.com/lots/12-front.jpg|https://cdn.example.com/lots/12-back.jpg
  ```

For every URL the import:

1. validates it: `http` / `https` only, and the host must resolve to a public address (private, loopback and link-local hosts are refused). Up to 3 redirects are followed, each hop validated the same way;
2. downloads it once (30 seconds per download, 25 MB max; variables `server_migrate_image_timeout` and `server_migrate_image_max_bytes`, `0` = no cap). The response must be an image;
3. generates the **big** (`gallery-big`) and **small** (`gallery-small`) derivatives locally and stores them on the site's image storage (S3 in production) under `SaleImages/csv-import/<sale nid>/<lot>_<position>.<ext>` — files are named after the lot (`12_1.jpg`, `12A_2.jpg`; `row-<unique id>` for a lot-less row). Formats GD cannot write (e.g. BMP) become JPG;
4. discards the original. The big derivative is the item's image file; the source URL is kept on the file and is served as the **zoom** (full size) image on the website / bid server, so the original stays at its external location.

Failures are per image: a URL that is refused, times out, is too large, is not an image or cannot be read is reported on the row (**Info** column, with the row number and the URL) and in the sale's import log, and the row is still saved with the images that succeeded.

### Re-import

When an item is imported again (**Queue update All items data**):

* an empty or missing `image_url` cell leaves the item's images alone (images uploaded manually are kept);
* a cell equal to the source URLs of the item's current images keeps them — nothing is downloaded again;
* a changed cell (a URL added, removed or reordered) downloads the new set and replaces the item's images; the files no longer referenced are deleted from the storage;
* when every image of a changed cell fails, the current images are kept.

{% hint style="info" %}
The status-only update (the single-field update of `_field_item_status`) never touches the images.
{% endhint %}

## Pricing Fields

* **`_opening_price`** - Opening bid price
* **`_minimum_price`** - Reserve/minimum price
* **`_reserve_price`** (or **`_reserve`**) - Reserve price
* **`_reserve_type`** - Reserve type: `soft`, `net` or `gross`, or the labels `Soft Reserve`, `Hard Reserve - Net`, `Hard Reserve - Gross` (`hard` means `net`). A reserve price without a type is a soft reserve; without a reserve price the type is cleared.
* **`_estimation_low`** - Low estimate
* **`_estimation_high`** - High estimate
* **`_buy_now_price`** - Buy now price
* **`_buy_now_setting`** - Buy now setting

{% hint style="info" %}
Currency symbols will be automatically removed and numbers converted to proper float format (opening, minimum, buy now and reserve prices).
{% endhint %}

## Category Fields

* **`_main_category`** - Main category name (pipe-separated for multiple)
* **`_main_category_{language}`** - Main category in second language
* **`_sub_category`** - Sub category (child of main category)
* **`_sub_category_{language}`** - Sub category in second language
* **`_extra_category`** - Extra category (child of sub category)
* **`_extra_category_{language}`** - Extra category in second language
* **`_main_category_id`** - HK / Philaworksplace category ID (matched against the category term's `field_hk_cat_id`)

{% hint style="info" %}
Categories support hierarchical structure: Main > Sub > Extra. Terms are auto-created if they don't exist (an existing term is only reused under the same parent). When `server_migrate_map_categories_from_migrate` is enabled, `_main_category` is resolved through the categories migration instead.
{% endhint %}

## Catalog Fields

Supports up to 2 catalogs per item:

**First Catalog:**
* **`_catalog_name`** - Catalog name (e.g., "Scott", "Stanley Gibbons")
* **`_catalog_number`** - Catalog number
* **`_catalog_number_value`** - Catalog value/price

**Second Catalog:**
* **`_catalog_name_2`** - Second catalog name
* **`_catalog_number_2`** - Second catalog number
* **`_catalog_number_value_2`** - Second catalog value/price

**Catalog Part:**
* **`_catalog_part`** - Catalog part/section (a `catalogue_part` term of the sale, created when missing)

## Philatelic Symbols

* **`_symbols`** - Philatelic symbols (pipe-separated)
  * Supports special notation:
    * `**` → Mint
    * `*` → Unused
    * `o` or `O` → Used
    * `(*)` → Without gum
  * Or use text names directly (e.g., "Mint|Used"); names are stored with a capital first letter and created in the symbols vocabulary when missing
* **`_sub_symbol`** - Sub symbol (only read when `_symbols` is given)

## Physical Properties

* **`_year`** - Year
* **`_country`** - Country
* **`_mediums`** - Mediums (pipe-separated for multiple values; unknown names create the term, case-insensitive)
* **`_grade`** - Grade/condition
* **`_thematics`** - Thematic categories

## Trading Cards

For trading-card sites the item form shows these fields under card labels (Subset, Card #, Player, Parallel /#). Sport, Brand and Set are the three category levels (`_main_category`, `_sub_category`, `_extra_category`) and the grading company (PSA, BGS, ...) goes in `_mediums`.

* **`_subset`** - Subset (stored in `field_art_work_type`; the term is created when missing)
* **`_card_number`** - Card number (stored in `field_caption`)
* **`_player`** - Player (stored in `field_literature`)
* **`_parallel`** - Parallel /# (stored in `field_essay`)

## Dimensions

**Individual Dimensions:**
* **`_height`** - Height
* **`_width`** - Width
* **`_depth`** - Depth
* **`_units`** - Units for the dimensions (`cm`, `mm`, `m` or `in`); optional
* **`_size_description`** - Description of dimensions

**Combined Dimensions:**
* **`_size`** - Format: "H x W x D unit" (e.g., "35.75 x 26.75 x 1.25 in")
* **`_framed`** - Framed dimensions (same format as `_size`)
* **`_quantity`** - Number of items (creates multiple dimension records)

{% hint style="info" %}
When no unit is given (no `_units` column, and no unit inside `_size` / `_framed`), the site's **Default dimension units** setting applies (Server Settings → Date & Currency Format). Until that setting is saved, it defaults to **metric (cm)** — except on US / CA installations (by the site's accounting country), which default to inches. The same default is used for new dimensions on the item form. The system automatically creates dimension nodes and parses combined dimension formats.
{% endhint %}

## Links & Certificates

**Certificate:**
* **`_certificate_link`** - Certificate URL
* **`_certificate_link_title`** - Certificate link title

**References:**
* **`_reference_link1`** - First reference URL
* **`_reference_link_title1`** - First reference link title
* **`_reference_link2`** - Second reference URL (only read when `_reference_link1` is given)
* **`_reference_link_title2`** - Second reference link title

## Item Settings

* **`_item_type`** - Item type (defaults to "single")
* **`_field_item_status`** - Item status (defaults to "ready"). On the Kedem installation the Hebrew status labels of the sheet are mapped to the item statuses; an unknown label skips the row.
* **`_collectible_type`** - Collectible type:
  * `Artwork` → converted to `fine_art`
  * `Book` → converted to `books`
  * Or use direct values: `stamps`, `coins`, etc.
  * Empty → the site's **Default collectible type** setting (`server_item_default_collectible_type`, `stamps` by default)
* **`_language`** - Not read: the item language is always the site's default language. Translations come from the `_..._{language}` columns of the configured second language.

## Additional Information

* **`_auctioneer_notes`** - Internal auctioneer notes
* **`_search_tag`** - Search tags for better discoverability
* **`_public_message`** - Public message visible to bidders
* **`_post_sale_purchase`** - Post-sale purchase flag
* **`_responsible_user`** - Responsible user (username, or a full name whose first word is used as the username)

## Commission

* **`_consignor_commission`** - Consignor commission percentage (on the item)
* **`_commission`** - Commission for a consignment the import creates (converted from decimal to percentage if < 1); see [Consignment & Sale Fields](#consignment--sale-fields) for what applies when the column is empty

## Consignor Auto-Creation Fields

When consignor doesn't exist, these fields enable auto-creation:

* **`consignor_email`** - Consignor email address (filled from `_consignment` when its first segment is an email)
* **`consignor_first_name`** - First name
* **`consignor_last_name`** - Last name
* **`consignor_full_name`** - Full name (will be split into first/last; filled from the consignment name segment of `_consignment` when given)

An existing client with the same first and last name is reused instead of creating a new one.

{% hint style="warning" %}
In non-live environments, ".test" is appended to emails to prevent sending to real clients.
{% endhint %}

## Notes (Self-Service Only)

`ServerItemsSelfServiceMigrate` also imports up to four free-form note columns and creates `note` nodes attached to the item:

* **`_notes`**, **`_notes2`**, **`_notes3`**, **`_notes4`** - Pipe-separated note strings. Each pipe segment becomes a separate note node. Titles are stable so re-imports do not create duplicates. The columns are read in order and reading stops at the first empty one (`_notes2` is ignored when `_notes` is empty).

## Sold Items (Creates Winning Bid)

When `_sold_for` is provided, the system automatically creates an item history task to record the winning bid:

* **`_sold_for`** - Sold price (triggers automatic item history task creation)
  * If `0`, item status is set to UNSOLD
  * If empty, no task is created
  * If > 0, item status is set to SOLD

**Winning Bidder Information:**
* **`_winning_user`** - Winning user internal ID (resolved through the clients migration when `server_migrate_map_winning_user_from_migrate` is enabled)
* **`_winning_user_nid`** - Winning user node ID
* **`_bidder_number`** - Bidder number for the sale

**Optional Client Information (for winner):**
* **`_first_name`** - Winner's first name
* **`_last_name`** - Winner's last name
* **`_phone`** - Winner's phone number
* **`_email`** - Winner's email address

{% hint style="info" %}
**Item History Queue Task:**
The system queues a task (SERVER_ITEM_HISTORY_QUEUE_IMPORT_RESULT) that:
- Creates the winning bid record
- Links the item to the winning bidder
- Records the sold price and status
- Allows empty bidder (auto-creates if needed)
- Uses lot_number and lot_letter ('-' default) to identify the item
{% endhint %}

## Processing Logic

1. **Consignment Resolution:**
   * Searches by consignment id → consignment name → consignor (customer id / `nid:` / email) → the consignor's consignment in the sale
   * Auto-creates consignor if email and name provided
   * Auto-creates consignment if consignor found but no matching consignment

2. **Price Conversion:**
   * Removes currency symbols automatically
   * Converts to proper float format

3. **Category Hierarchy:**
   * Creates multi-level taxonomy: Main → Sub → Extra
   * Supports multi-language term names
   * Auto-creates missing terms

4. **Dimension Nodes:**
   * Creates separate dimension node entities
   * Supports multiple instances based on `_quantity`
   * Parses combined dimension strings automatically

5. **HTML Cleaning:**
   * Body, provenance, and footer fields are cleaned of unsafe HTML

6. **Symbol Mapping:**
   * Converts philatelic shorthand to full term names
   * Creates terms if they don't exist in symbols vocabulary

7. **Images:**
   * Downloaded from the `image_url` column inside the queued import, before the item is saved, so each row is saved once with its images
   * The replaced files of a changed cell are deleted after the save

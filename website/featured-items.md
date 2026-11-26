# Featured Items

**Selector:** `#ca-featured-items` &nbsp;•&nbsp; *one per page*

## What it does

Displays the items tagged **Featured item**, as an autoplaying carousel or as a grid. It shows the featured items of one sale, or, when no sale is given, of every sale that is published on site. Optionally it can show the sale's image or full sale information above the items. It is ideal for a homepage highlight strip or the top of a sale landing page.

In every layout the item image comes first; the lot number and the rest of the lot information sit below it.

The carousel advances every 8 seconds, pauses on hover, and offers play/pause and previous/next controls plus keyboard navigation (left/right to move, space to pause). If there are no featured items, the block renders nothing.

## How to add it

```html
<!-- Featured items of one sale -->
<div id="ca-featured-items" class="circuit-user-ui" data-sale-nid="123"></div>

<!-- Featured items of every sale that is published on site -->
<div id="ca-featured-items" class="circuit-user-ui"></div>
```

## Options

| Attribute | Required | Default | Values | Effect |
|-----------|----------|---------|--------|--------|
| `data-sale-nid` | No | *(none)* | sale NID | Which sale's featured items to show. Omit it to show the featured items of every sale that is published on site (see below). |
| `data-layout` | No | *(none)* | `grid` | How the items are laid out. Omit for the carousel; `grid` shows all items side by side, at most 4 per row, with no carousel controls or autoplay. |
| `data-display-mode` | No | *(none)* | `sale-image`, `sale-info` | What to show above the items. Omit for items only; `sale-image` adds the clickable sale logo; `sale-info` adds logo, title, date/location, online‑catalog link and PDF links. Needs `data-sale-nid`. |
| `data-item-display` | No | *(none)* | `lot-number` | How each item looks. Omit for the full display (image, lot number, title, pricing, bidding). `lot-number` shows just the image with the lot number below it. |

`data-layout` and `data-item-display` are independent, so any combination works: a carousel or a grid, each with full items or with lot numbers only.

### Without a sale NID

When `data-sale-nid` is omitted, the block collects the featured items of every sale whose status is *published on site*:

- Sales are ordered by sale date, soonest first; within a sale the lots are in lot‑number order.
- Each item shows its sale number next to the lot number (for example "Lot #12 – Sale 383"), because the lots come from several sales. With a sale NID only the lot number is shown.
- `data-display-mode` is ignored: there is no single sale to show above the items.

### Grid layout

With `data-layout="grid"` the items are shown as tiles in a responsive grid: up to 4 per row on wide screens, fewer as the screen narrows, and a single column on small phones.

## Examples

```html
<!-- Carousel only -->
<div id="ca-featured-items" class="circuit-user-ui" data-sale-nid="123"></div>

<!-- Homepage highlight: full sale info + minimal item tiles -->
<div id="ca-featured-items" class="circuit-user-ui"
     data-sale-nid="123"
     data-display-mode="sale-info"
     data-item-display="lot-number"></div>

<!-- Just the sale image above the carousel -->
<div id="ca-featured-items" class="circuit-user-ui"
     data-sale-nid="123"
     data-display-mode="sale-image"></div>

<!-- Grid of one sale's featured items -->
<div id="ca-featured-items" class="circuit-user-ui"
     data-sale-nid="123"
     data-layout="grid"></div>

<!-- Grid of image + lot number tiles across every sale that is published on site -->
<div id="ca-featured-items" class="circuit-user-ui"
     data-layout="grid"
     data-item-display="lot-number"></div>
```

> **Tip:** The [Sale Page](website/sale-page.md) block can embed this carousel automatically with `data-show-featured="true"`, and the [Sales List](website/sales-list.md) block can show one per sale with `data-show-featured-items="true"`.

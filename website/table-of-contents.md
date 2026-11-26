# Table of Contents

**Selector:** `#ca-table-of-contents` &nbsp;•&nbsp; *one per page*

## What it does

A standalone page for one sale's table of contents. The top of the page shows the sale itself: logo, title, subtitle, date and location, and the full description. Below it, every session of the sale is listed with its name, date and lot range. Sub‑sessions are indented under their parent session.

The list is always fully open: there is nothing to expand and no inner scrolling, however many sessions the sale has. Each session links to the sale page filtered to that session's lots, and the logo and title link to the sale page itself.

The [Sale Page](website/sale-page.md) shows the same list as a collapsed "Table of Contents" section. Use this block when you want the table of contents as a page of its own, for example as a "Sale overview" link in your menu.

## How to add it

```html
<div id="ca-table-of-contents" class="circuit-user-ui" data-sale-nid="123"></div>
```

## Options

| Attribute | Required | Effect |
|-----------|----------|--------|
| `data-sale-nid` | No | The NID of the sale to display. If omitted, the sale is taken from the `saleNid` URL parameter. |

This block has no other options.

### Choosing the sale

The sale is identified by its NID, from either of two places:

1. The `data-sale-nid` attribute on the block.
2. The `saleNid` parameter in the page URL, for example `/table-of-contents?saleNid=123`.

The attribute wins when both are present. If neither is given, the block renders nothing.

The URL parameter lets a single page on your website serve every sale: add the block without `data-sale-nid` and link to that page with a different `saleNid` for each sale.

## Examples

```html
<!-- Table of contents of one fixed sale -->
<div id="ca-table-of-contents" class="circuit-user-ui" data-sale-nid="123"></div>

<!-- One page for all sales: /table-of-contents?saleNid=123 -->
<div id="ca-table-of-contents" class="circuit-user-ui"></div>
```

> A sale without sessions shows the sale information only.

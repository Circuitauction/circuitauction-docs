# Website (Embeddable Blocks)

> If you are setting up a WordPress site, the [CircuitAuction WordPress plugin](wordpress-plugin.md) automates everything on this page (loader script, setup div, page divs) and adds Gutenberg blocks for the catalog and sales widgets.

The **Circuit User UI** is a React application that powers the bidder‑facing parts of your website — sale listings, item pages, bidding, account management, billing and more. Instead of being a single page, it is delivered as a set of **blocks** (also called components). You drop a small `<div>` onto any page of your own website and the matching block renders inside it.

This lets you build a fully branded auction website using your own CMS (WordPress, Drupal, plain HTML, etc.) while Circuit handles the catalog, bidding and account logic.

## How embedding works

There are three ingredients on any page that uses a block:

1. **The loader script** — added once, in the page `<head>`. It pulls in the app code and styles.
2. **The setup div** — `#circuit-setup`, added once per page. It tells the app which site/back office to talk to.
3. **One or more block divs** — each one renders a specific block where you place it.

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <!-- 1. Load the Circuit User UI -->
  <script src="https://cdn.circuitauction.com/stable/user-ui/app-loader.js"></script>
</head>
<body>

  <!-- 2. Tell the app which site it belongs to -->
  <div id="circuit-setup"
       site="backoffice-fifthavenue"
       hostname="test-fifthavenue.circuit.auction"></div>

  <!-- 3. Add any blocks you want on this page -->
  <div id="ca-sales-list" class="circuit-user-ui"></div>

</body>
</html>
```

### The setup div (`#circuit-setup`)

A single `#circuit-setup` div configures every block on the page.

| Attribute | Required | Description |
|-----------|----------|-------------|
| `site` | Yes | The site machine name (the back‑office identifier for this auction house). |
| `hostname` | Yes | The Circuit back‑office hostname the blocks should connect to. |
| `language` | No | Forces the interface language. One of `en`, `fr`, `de`, `nl`, `ru`, `zh`. If omitted, the app uses the visitor's saved preference or browser language, falling back to English. |

> The setup div renders nothing visible — it only carries configuration.

### The `circuit-user-ui` class

Add `class="circuit-user-ui"` to every block div. The app's styles are scoped to this class so that they apply **only inside the blocks** and never leak into (or get overridden by) your site's theme.

```html
<div id="my-bids" class="circuit-user-ui"></div>
```

### Element ID vs. class

Most blocks are matched by their **element `id`** (e.g. `id="ca-sale-page"`), so each of those can appear **once per page**. A few blocks — **Place Bid** and **Favorite Button** — are matched by **class** instead, so you can place many of them on the same page (e.g. one bid widget per item in a list).

## Available blocks

### Catalog & sales blocks

These blocks display public auction content and accept display options as `data-*` attributes.

| Block | Selector | Purpose |
|-------|----------|---------|
| [Sales List](website/sales-list.md) | `#ca-sales-list` | Filterable, paginated list of sales |
| [Sale Page](website/sale-page.md) | `#ca-sale-page` | Searchable item listing for one sale |
| [Sale Info](website/sale-info.md) | `#ca-sale-info` | Standalone information block for one sale |
| [Table of Contents](website/table-of-contents.md) | `#ca-table-of-contents` | Standalone table of contents (sessions) for one sale |
| [Featured Items](website/featured-items.md) | `#ca-featured-items` | Carousel or grid of featured items, for one sale or all published sales |
| [Item Page](website/item-page.md) | `#ca-item-page` | Single item with details and bidding |
| [Prices Realized](website/prices-realized.md) | `#ca-prices-realized` | Results table for a completed sale |
| [Place Bid](website/place-bid.md) | `.place-bid` | Inline bidding widget (multiple per page) |
| [Favorite Button](website/favorite-button.md) | `.favorite-btn` | Add/remove an item from favorites |

### Account & user blocks

These blocks handle the logged‑in bidder experience. Most read no options — they render based on the signed‑in user.

| Block | Selector | Purpose |
|-------|----------|---------|
| [User Menu](website/user-block.md) | `#user-block` | Sign‑in links or the logged‑in user dropdown |
| [Login](website/login.md) | `#login` | Sign‑in form |
| [Register](website/register.md) | `#register` | New account registration form |
| [Forgot Password](website/forgot-password.md) | `#forgotpassword` | Password reset request form |
| [My Account](website/my-account.md) | `#my-account` | Profile, password and addresses |
| [My Bids](website/my-bids.md) | `#my-bids` | Active and past bids, with credit |
| [My Favorites](website/my-favorites.md) | `#my-favorites` | Saved items, with compare |
| [My Interests](website/my-interests.md) | `#my-interests` | Category interest preferences |
| [Balance Button](website/balance.md) | `#balance-btn` | Account balance / amount due |
| [Billing & Payments](website/billing.md) | `#billing-history` | Invoices and saved payment methods |
| [Magazine Subscription](website/subscription-magazine.md) | `#subscription-magazine` | Magazine flipbook library |
| [Purchase Subscription](website/subscription-purchase.md) | `#subscription-purchase` | Subscription plan picker |

## A note on attribute values

- **Boolean‑style attributes** are read as strings. Use `data-show-filters="true"` / `"false"` (or `"1"`). The literal text `"true"`/`"1"` enables; anything else disables.
- **IDs and UUIDs** come from the back office. A *NID* is the numeric node ID of a sale or item; a *UUID* is the long string identifier (e.g. `live-68f657cdd8e3a5-18178142`). Each block's page notes which it expects.
</content>
</invoke>

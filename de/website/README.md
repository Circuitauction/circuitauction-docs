<!-- i18n source=website/README.md sha=78068a32ce1a -->
# Website (einbettbare Blöcke)

> Wenn Sie eine WordPress-Website einrichten, automatisiert das [CircuitAuction-WordPress-Plugin](/de/website/wordpress-plugin.md) alles auf dieser Seite (Loader-Skript, Setup-Div, Seiten-Divs) und ergänzt Gutenberg-Blöcke für die Katalog- und Auktions-Widgets.

Die **Circuit User UI** ist eine React-Anwendung, die die Bieter-Bereiche Ihrer Website mit Leben füllt — Auktionslisten, Losseiten, Bieten, Kontoverwaltung, Abrechnung und mehr. Statt einer einzelnen Seite wird sie als Satz von **Blöcken** (auch Komponenten genannt) ausgeliefert. Sie platzieren ein kleines `<div>` auf einer beliebigen Seite Ihrer eigenen Website, und der passende Block wird darin dargestellt.

So können Sie mit Ihrem eigenen CMS (WordPress, Drupal, reines HTML usw.) eine vollständig im eigenen Design gehaltene Auktionswebsite aufbauen, während Circuit die Katalog-, Gebots- und Kontologik übernimmt.

## So funktioniert die Einbettung

Auf jeder Seite, die einen Block verwendet, gibt es drei Bestandteile:

1. **Das Loader-Skript** — einmal im `<head>` der Seite eingefügt. Es lädt den App-Code und die Stile.
2. **Das Setup-Div** — `#circuit-setup`, einmal pro Seite eingefügt. Es teilt der App mit, mit welcher Website bzw. welchem Backoffice sie kommunizieren soll.
3. **Ein oder mehrere Block-Divs** — jedes stellt an der Stelle, an der Sie es platzieren, einen bestimmten Block dar.

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

### Das Setup-Div (`#circuit-setup`)

Ein einziges `#circuit-setup`-Div konfiguriert alle Blöcke der Seite.

| Attribut | Erforderlich | Beschreibung |
|-----------|----------|-------------|
| `site` | Ja | Der Maschinenname der Website (die Backoffice-Kennung dieses Auktionshauses). |
| `hostname` | Ja | Der Hostname des Circuit-Backoffice, mit dem sich die Blöcke verbinden sollen. |
| `language` | Nein | Erzwingt die Sprache der Oberfläche. Eines von `en`, `fr`, `de`, `nl`, `ru`, `zh`. Ohne Angabe verwendet die App die gespeicherte Einstellung oder die Browsersprache des Besuchers und fällt auf Englisch zurück. |

> Das Setup-Div stellt nichts Sichtbares dar — es enthält nur Konfiguration.

### Die Klasse `circuit-user-ui`

Fügen Sie jedem Block-Div `class="circuit-user-ui"` hinzu. Die Stile der App sind auf diese Klasse begrenzt, sodass sie **nur innerhalb der Blöcke** gelten und weder in das Theme Ihrer Website hineinwirken noch von diesem überschrieben werden.

```html
<div id="my-bids" class="circuit-user-ui"></div>
```

### Element-ID vs. Klasse

Die meisten Blöcke werden über ihre **Element-`id`** gefunden (z. B. `id="ca-sale-page"`), weshalb jeder von ihnen **einmal pro Seite** vorkommen darf. Einige wenige Blöcke — **Gebot abgeben** und **Favoriten-Button** — werden stattdessen über die **Klasse** gefunden, sodass Sie viele davon auf derselben Seite platzieren können (z. B. ein Gebots-Widget pro Los in einer Liste).

## Verfügbare Blöcke

### Katalog- und Auktionsblöcke

Diese Blöcke zeigen öffentliche Auktionsinhalte an und akzeptieren Anzeigeoptionen als `data-*`-Attribute.

| Block | Selektor | Zweck |
|-------|----------|---------|
| [Auktionsliste](/de/website/sales-list.md) | `#ca-sales-list` | Filterbare Liste von Auktionen mit Seitennavigation |
| [Auktionsseite](/de/website/sale-page.md) | `#ca-sale-page` | Durchsuchbare Losliste einer Auktion |
| [Auktionsinfo](/de/website/sale-info.md) | `#ca-sale-info` | Eigenständiger Informationsblock für eine Auktion |
| [Inhaltsverzeichnis](/de/website/table-of-contents.md) | `#ca-table-of-contents` | Eigenständiges Inhaltsverzeichnis (Sessions) einer Auktion |
| [Hervorgehobene Lose](/de/website/featured-items.md) | `#ca-featured-items` | Karussell oder Raster der Highlight-Lose, für eine Auktion oder alle veröffentlichten Auktionen |
| [Losseite](/de/website/item-page.md) | `#ca-item-page` | Einzelnes Los mit Details und Bieten |
| [Erzielte Preise](/de/website/prices-realized.md) | `#ca-prices-realized` | Ergebnistabelle einer abgeschlossenen Auktion |
| [Gebot abgeben](/de/website/place-bid.md) | `.place-bid` | Inline-Gebots-Widget (mehrfach pro Seite) |
| [Favoriten-Button](/de/website/favorite-button.md) | `.favorite-btn` | Ein Los zu den Favoriten hinzufügen/daraus entfernen |

### Konto- und Benutzerblöcke

Diese Blöcke bilden die Erfahrung des angemeldeten Bieters ab. Die meisten lesen keine Optionen — sie werden anhand des angemeldeten Benutzers dargestellt.

| Block | Selektor | Zweck |
|-------|----------|---------|
| [Benutzermenü](/de/website/user-block.md) | `#user-block` | Anmeldelinks oder das Dropdown des angemeldeten Benutzers |
| [Anmeldung](/de/website/login.md) | `#login` | Anmeldeformular |
| [Registrierung](/de/website/register.md) | `#register` | Registrierungsformular für neue Konten |
| [Passwort vergessen](/de/website/forgot-password.md) | `#forgotpassword` | Formular zum Anfordern des Zurücksetzens des Passworts |
| [Mein Konto](/de/website/my-account.md) | `#my-account` | Profil, Passwort und Adressen |
| [Meine Gebote](/de/website/my-bids.md) | `#my-bids` | Aktive und vergangene Gebote, mit Guthaben |
| [Meine Favoriten](/de/website/my-favorites.md) | `#my-favorites` | Gespeicherte Lose, mit Vergleich |
| [Meine Interessen](/de/website/my-interests.md) | `#my-interests` | Einstellungen zu Kategorieinteressen |
| [Saldo-Button](/de/website/balance.md) | `#balance-btn` | Kontosaldo / fälliger Betrag |
| [Abrechnung & Zahlungen](/de/website/billing.md) | `#billing-history` | Rechnungen und gespeicherte Zahlungsmethoden |
| [Magazin-Abonnement](/de/website/subscription-magazine.md) | `#subscription-magazine` | Flipbook-Bibliothek der Magazine |
| [Abonnement kaufen](/de/website/subscription-purchase.md) | `#subscription-purchase` | Auswahl der Abonnement-Pläne |

## Hinweis zu Attributwerten

- **Attribute im Boolean-Stil** werden als Strings gelesen. Verwenden Sie `data-show-filters="true"` / `"false"` (oder `"1"`). Der Text `"true"`/`"1"` aktiviert; alles andere deaktiviert.
- **IDs und UUIDs** stammen aus dem Backoffice. Eine *NID* ist die numerische Node-ID einer Auktion oder eines Loses; eine *UUID* ist die lange Zeichenfolge zur Identifikation (z. B. `live-68f657cdd8e3a5-18178142`). Auf der Seite jedes Blocks steht, welche davon erwartet wird.

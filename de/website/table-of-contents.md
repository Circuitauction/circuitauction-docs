<!-- i18n source=website/table-of-contents.md sha=035583a6e356 -->
# Inhaltsverzeichnis

**Selektor:** `#ca-table-of-contents` &nbsp;•&nbsp; *einmal pro Seite*

## Funktion

Eine eigenständige Seite für das Inhaltsverzeichnis einer Auktion. Oben zeigt die Seite die Auktion selbst: Logo, Titel, Untertitel, Datum und Ort sowie die vollständige Beschreibung. Darunter ist jede Session der Auktion mit Name, Datum und Losbereich aufgeführt. Unter-Sessions sind unter ihrer übergeordneten Session eingerückt.

Die Liste ist immer vollständig geöffnet: Es gibt nichts aufzuklappen und kein inneres Scrollen, ganz gleich, wie viele Sessions die Auktion hat. Jede Session verlinkt zur Auktionsseite, gefiltert auf die Lose dieser Session; Logo und Titel verlinken zur Auktionsseite selbst.

Die [Auktionsseite](/de/website/sale-page.md) zeigt dieselbe Liste als eingeklappten Abschnitt „Inhaltsverzeichnis“. Verwenden Sie diesen Block, wenn Sie das Inhaltsverzeichnis als eigene Seite anbieten möchten, zum Beispiel als Link „Auktionsübersicht“ in Ihrem Menü.

## So fügen Sie es hinzu

```html
<div id="ca-table-of-contents" class="circuit-user-ui" data-sale-nid="123"></div>
```

## Optionen

| Attribut | Erforderlich | Wirkung |
|-----------|----------|--------|
| `data-sale-nid` | Nein | Die NID der Auktion, die angezeigt werden soll. Ohne Angabe wird die Auktion aus dem URL-Parameter `saleNid` übernommen. |

Dieser Block hat keine weiteren Optionen.

### Auswahl der Auktion

Die Auktion wird über ihre NID bestimmt, die aus einer von zwei Quellen stammt:

1. Dem Attribut `data-sale-nid` am Block.
2. Dem Parameter `saleNid` in der Seiten-URL, zum Beispiel `/table-of-contents?saleNid=123`.

Sind beide vorhanden, hat das Attribut Vorrang. Ist keines von beiden angegeben, stellt der Block nichts dar.

Mit dem URL-Parameter kann eine einzige Seite Ihrer Website alle Auktionen bedienen: Fügen Sie den Block ohne `data-sale-nid` ein und verlinken Sie für jede Auktion mit einer anderen `saleNid` auf diese Seite.

## Beispiele

```html
<!-- Table of contents of one fixed sale -->
<div id="ca-table-of-contents" class="circuit-user-ui" data-sale-nid="123"></div>

<!-- One page for all sales: /table-of-contents?saleNid=123 -->
<div id="ca-table-of-contents" class="circuit-user-ui"></div>
```

> Eine Auktion ohne Sessions zeigt nur die Auktionsinformationen.

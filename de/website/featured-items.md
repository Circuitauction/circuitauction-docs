<!-- i18n source=website/featured-items.md sha=f84872e6a215 -->
# Hervorgehobene Lose

**Selektor:** `#ca-featured-items` &nbsp;•&nbsp; *einmal pro Seite*

## Funktion

Zeigt die Lose, die als **Featured item** markiert sind, als automatisch ablaufendes Karussell oder als Raster. Angezeigt werden die Highlight-Lose einer Auktion oder, wenn keine Auktion angegeben ist, aller Auktionen, die auf der Website veröffentlicht sind. Optional kann der Block über den Losen das Auktionsbild oder die vollständige Auktionsinformation anzeigen. Er eignet sich ideal für einen Highlight-Streifen auf der Startseite oder den oberen Bereich einer Auktions-Landingpage.

In jedem Layout steht das Bild des Loses oben; die Losnummer und die übrigen Angaben zum Los folgen darunter.

Das Karussell wechselt alle 8 Sekunden, pausiert beim Überfahren mit der Maus und bietet Schaltflächen für Wiedergabe/Pause und Zurück/Weiter sowie Tastaturbedienung (links/rechts zum Blättern, Leertaste zum Pausieren). Gibt es keine Highlight-Lose, stellt der Block nichts dar.

## So fügen Sie es hinzu

```html
<!-- Featured items of one sale -->
<div id="ca-featured-items" class="circuit-user-ui" data-sale-nid="123"></div>

<!-- Featured items of every sale that is published on site -->
<div id="ca-featured-items" class="circuit-user-ui"></div>
```

## Optionen

| Attribut | Erforderlich | Standard | Werte | Wirkung |
|-----------|----------|---------|--------|--------|
| `data-sale-nid` | Nein | *(keiner)* | Auktions-NID | Von welcher Auktion die Highlight-Lose angezeigt werden. Ohne Angabe werden die Highlight-Lose aller Auktionen angezeigt, die auf der Website veröffentlicht sind (siehe unten). |
| `data-layout` | Nein | *(keiner)* | `grid` | Wie die Lose angeordnet werden. Ohne Angabe das Karussell; `grid` zeigt alle Lose nebeneinander, höchstens 4 pro Zeile, ohne Karussell-Steuerung und ohne automatischen Wechsel. |
| `data-display-mode` | Nein | *(keiner)* | `sale-image`, `sale-info` | Was über den Losen angezeigt wird. Ohne Angabe nur die Lose; `sale-image` fügt das anklickbare Auktionslogo hinzu; `sale-info` fügt Logo, Titel, Datum/Ort, Link zum Online-Katalog und PDF-Links hinzu. Benötigt `data-sale-nid`. |
| `data-item-display` | Nein | *(keiner)* | `lot-number` | Wie jedes Los aussieht. Ohne Angabe die vollständige Darstellung (Bild, Losnummer, Titel, Preise, Gebotsabgabe). `lot-number` zeigt nur das Bild mit der Losnummer darunter. |

`data-layout` und `data-item-display` sind voneinander unabhängig, sodass jede Kombination funktioniert: Karussell oder Raster, jeweils mit vollständigen Losen oder nur mit Losnummern.

### Ohne Auktions-NID

Wird `data-sale-nid` weggelassen, sammelt der Block die Highlight-Lose aller Auktionen mit dem Status *auf der Website veröffentlicht*:

- Die Auktionen sind nach Auktionsdatum sortiert, die nächste zuerst; innerhalb einer Auktion stehen die Lose in der Reihenfolge der Losnummern.
- Jedes Los zeigt neben der Losnummer seine Auktionsnummer (zum Beispiel „Los #12 – Auktion 383“), weil die Lose aus mehreren Auktionen stammen. Mit einer Auktions-NID wird nur die Losnummer angezeigt.
- `data-display-mode` wird ignoriert: Es gibt keine einzelne Auktion, die über den Losen angezeigt werden könnte.

### Raster-Layout

Mit `data-layout="grid"` werden die Lose als Kacheln in einem responsiven Raster angezeigt: bis zu 4 pro Zeile auf breiten Bildschirmen, weniger auf schmaleren und eine einzelne Spalte auf kleinen Smartphones.

## Beispiele

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

> **Tipp:** Der Block [Auktionsseite](/de/website/sale-page.md) kann dieses Karussell mit `data-show-featured="true"` automatisch einbetten, und der Block [Auktionsliste](/de/website/sales-list.md) kann mit `data-show-featured-items="true"` pro Auktion eines anzeigen.

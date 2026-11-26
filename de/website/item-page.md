<!-- i18n source=website/item-page.md sha=ef21935815da -->
# Losseite

**Selektor:** `#ca-item-page` &nbsp;•&nbsp; *einmal pro Seite*

## Funktion

Zeigt ein einzelnes Auktionslos: Bildergalerie mit Lightbox, Titel und Untertitel, Los- und Artikelnummern, vollständige Beschreibung, Schätzung und aktuelles bzw. Ausrufgebot, Aufgeld, Brotkrumennavigation, Favoriten- und Teilen-Buttons, ein Formular „Ask a Question“, verwandte Lose und die Gebotsoberfläche. Mehrere Anzeigemodi erlauben es, denselben Block als vollständige Detailseite oder als kompakte Karte zu verwenden.

## So fügen Sie es hinzu

```html
<div id="ca-item-page" class="circuit-user-ui" data-item-id="123456" data-display-mode="full" data-thumb-rows="2"></div>
```

## Optionen

| Attribut | Erforderlich | Standard | Werte | Wirkung |
|-----------|----------|---------|--------|--------|
| `data-item-id` | **Ja** | — | NID oder UUID | Welches Los angezeigt wird. Akzeptiert sowohl die numerische Node-ID als auch die UUID. |
| `data-display-mode` | Nein | `full` | `full`, `teaser`, `grid`, `featured`, `live-auction` | Wie das Los dargestellt wird (siehe unten). |
| `data-thumb-rows` | Nein | `1` | Zahl | Wie viele Zeilen von Galerie-Vorschaubildern vor dem Umschalter „Mehr anzeigen“ gezeigt werden. |

Bei Losen mit mehr als 200 Bildern öffnet der Umschalter die Vollbild-Bildansicht (mit eigener scrollbarer Vorschaubildleiste), statt die Vorschaubilder direkt auszuklappen.

### Verhalten der Galerie

Ein Klick auf ein Vorschaubild zeigt dieses Bild im Hauptbild oberhalb der Vorschaubilder. Bei einem Los mit vielen Bildern hat der Besucher die Vorschaubilder möglicherweise so weit nach unten gescrollt, dass das Hauptbild nicht mehr auf dem Bildschirm zu sehen ist. In diesem Fall öffnet der Klick stattdessen die Vollbild-Bildansicht mit diesem Bild, sodass der Besucher das gewählte Bild immer sieht.

Das Hauptbild folgt der Vollbild-Bildansicht: Wer dort zu einem anderen Bild blättert und die Ansicht schließt, sieht auf der Seite dasselbe Bild.

### Anzeigemodi

| Modus | Geeignet für | Zeigt |
|------|----------|-------|
| `full` | Eine eigene Losseite | Alles: Brotkrumennavigation, vollständige Galerie + Lightbox, vollständige Beschreibung, Preise, Gebote, Favorit/Teilen, Frage stellen, verwandte Lose. |
| `teaser` | Listenzeilen | Horizontale Karte: Bild, Titel, Losnummer, Schätzung, aktuelles bzw. Ausrufgebot, Favorit, Verkaufsstatus. |
| `grid` | Rasterlayouts | Vertikale Karte: Bild, gekürzter Titel, Losnummer, Schätzung oder aktuelles Gebot, Favorit, Verkaufsanzeige. |
| `featured` | Karussells | Großes Bild im Fokus, Titel, Schätzung, aktuelles Gebot und integrierte Gebotsabgabe. |
| `live-auction` | Live-Bietansichten | Aktuelles Gebot in Echtzeit, Steigerungsbuttons, Gebotsverlauf, Restzeit und Verkauft-Overlay. |

## Beispiele

```html
<!-- Full detail page -->
<div id="ca-item-page" class="circuit-user-ui"
     data-item-id="1902261" data-display-mode="full"></div>

<!-- Compact card in a custom grid -->
<div id="ca-item-page" class="circuit-user-ui"
     data-item-id="1902261" data-display-mode="grid"></div>
```

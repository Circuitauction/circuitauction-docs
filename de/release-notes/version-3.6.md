<!-- i18n source=release-notes/version-3.6.md sha=3e2df94604a9 -->
# Version 3.6

Kommendes Release. Version 3.6 folgt einem Los, wohin es auch geht: Ein Los, das in eine spätere Auktion geklont oder verschoben, an ein verbundenes Backoffice gesendet oder von dort zurückgeholt wird, zeigt jetzt seine gesamte Kette auf der Losseite, im Stammbaum und in der Losliste; sein Verlaufs-Tab liest über Backoffices hinweg, und die Regeln für Klonen und Verschieben stellen sicher, dass ein Los immer von seinem neuesten Glied genommen wird. Klon- und Verschiebe-Stapel laufen über die Warteschlange, ein Los nach dem anderen, und melden jedes Ergebnis auf der Losliste.

## Lose

### Die Kette eines Loses auf der Losseite und im Stammbaum

![Die Kette eines Loses unter der Los-Navigation](../../assets/screenshots/item-chain.png)

Jedes Klonen und jedes Verschieben fügt der **Kette** eines Loses ein Glied hinzu: die Auktionen, in denen es angeboten wurde, und die Backoffices, an die es gesendet wurde.

- Auf der Losseite wird die Kette unter der Los-Navigation als Reihe von Kästchen gezeichnet, die älteste Auktion zuerst, mit der Auktion hervorgehoben, in der das Los geöffnet ist. Ein dunkleres Kästchen ist ein verbundenes Backoffice. Jedes Kästchen öffnet das Los in dieser Auktion oder in diesem Backoffice.
- Der Kopfbereich des Loses zeigt **Cloned from** bei einem Los, das aus einem anderen Los oder von einem verbundenen Backoffice stammt.
- Im Tab **Alle Lose (Stammbaum)** einer Einlieferung wird dieselbe Kette in einer Zeile über jedem Los gezeichnet, das unterwegs war.
- Eine Kette ist von jedem Glied aus und in jedem Backoffice dieselbe: Das Backoffice fragt seine verbundenen Backoffices nach ihrem Teil der Kette.

Siehe [Los zwischen Auktionen und Backoffices klonen oder verschieben](/de/items/clone-move-between-sales-and-backoffices.md).

### Losverlauf über Backoffices hinweg

![Der Tab Losverlauf mit einer Zeile vom verbundenen Backoffice](../../assets/screenshots/item-history-external.png)

Der Tab **Losverlauf** listet die Verlaufszeilen jedes Glieds der Kette auf. Eine Zeile, die ein Verschieben zu oder von einem verbundenen Backoffice festhält, trägt neben dem Status das Kennzeichen des Backoffice, zu dem sie gehört, und verlinkt auf das Los im anderen Backoffice; sind die Backoffices verbunden, werden auch die Zeilen abgerufen und aufgelistet, die das andere Backoffice hält, mit dessen Kennzeichen. Eine neue Einstellung **Backoffice name** (Server Settings, General) benennt dieses Backoffice auf diesen Kennzeichen.

### Regeln für Klonen und Verschieben und „Force clone items“

![Der Block Los klonen/ verschieben mit einem abgelehnten Los und angehaktem Force clone items](../../assets/screenshots/clone-move-force-clone.png)

Ein Los wird nur vom **neuesten Glied seiner Kette** geklont oder verschoben, und nur solange es noch zum Verkauf steht:

- Ein Los, das bereits weitergeklont wurde oder an ein verbundenes Backoffice gesendet wurde, wird mit einer Meldung abgelehnt, die das neuere Los und den Tag des Klonens nennt, oder das Backoffice, an das es gesendet wurde.
- Nur Lose mit dem Status **unverkauft** und **Zum Verkauf** können geklont oder verschoben werden; ein Los mit dem Status **Verkauft** wird nie verschoben, weil seine Rechnung es verlieren würde.
- **Force clone items** hebt diese Regeln auf, nur für das Klonen.
- Das Popover **Clone/ Move information** (das Fragezeichen neben der Schaltfläche) listet jetzt diese Regeln zusammen mit den Bedingungen für ein Verschieben auf.

Lose, die vor dieser Version geklont wurden, funktionieren weiterhin: Ihre alten Einträge werden beim Update des Backoffice in die Kette übernommen.

### Klon- und Verschiebe-Stapel über die Warteschlange

Anfragen zum Klonen und Verschieben aus der Losliste laufen jetzt als eine Warteschlangen-Aufgabe je Los, in der Reihenfolge, in der die Lose gesendet wurden, sowohl in diesem Backoffice als auch zwischen verbundenen Backoffices. Der Block **Los klonen/ verschieben** verfolgt den Stapel live: Zähler für ausstehend, laufend, erledigt und fehlgeschlagen sowie eine Zeile je Los mit seinem Ergebnis und einem Link zum neuen Los. Ein Stapel, der an ein verbundenes Backoffice gesendet wurde, wartet dort als „pending“, bis ein Benutzer dieses Backoffice die Klon-Seite öffnet und ihn in eine Auktion seiner Wahl freigibt, mit den dort gewählten Optionen für Einlieferung, Nachlass und Rundung; ein nicht freigegebener Stapel verfällt nach sieben Tagen.

### Spalte „Moved to“ in der Losliste

![Die Losliste mit der Spalte Moved to](../../assets/screenshots/items-list-moved-to.png)

Eine neue Spalte **Moved to** der Losliste zeigt im Auktionskontext einer Auktion, wohin jedes Los gegangen ist: leer bei einem Los, das geblieben ist, ein Link auf den Klon in der späteren Auktion oder das verbundene Backoffice, an das es gesendet wurde. Fügen Sie `moved_to` zu **List of column in item table** in den Server Settings hinzu, um sie anzuzeigen.

## Datenmigration

### Verbundene Lose bei Kelleher

Lose, die in einer späteren AMS-Auktion erneut angeboten wurden, werden jetzt als Glieder der Los-Kette importiert statt als separate Verbindungseinträge, sodass migrierte Lose dieselbe Kette, dieselben Verlaufszeilen und dieselben Zellen in Moved to zeigen wie im Backoffice geklonte Lose.

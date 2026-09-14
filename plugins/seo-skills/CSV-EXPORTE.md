# Ohne MCP: die Skills mit CSV-Exporten nutzen

Alle sechs Routine-Skills laufen auch ohne verbundenen MCP-Server. Du
exportierst die Daten aus der Google-Oberfläche, fügst sie in den Chat ein
oder lädst die Datei hoch, und nennst den Skill wie gewohnt. Der Skill
erkennt am Kontext, dass er mit einem Export arbeitet.

Der Unterschied zum MCP-Weg in einem Satz: Mit Export ist der Datenstand
der Zeitpunkt des Exports, und jede weitere Frage braucht einen neuen
Export. Mit MCP stellst du die nächste Frage einfach im Chat. Die
Menünamen unten entsprechen der deutschen Google-Oberfläche vom September
2026 und können sich ändern.

## Google Search Console

**Leistungsbericht** (für `gsc-wochen-check`, `gsc-chancen-keywords`,
`gsc-kannibalisierung`): Leistung → oben rechts „Exportieren" → CSV. Der
Download ist ein ZIP mit mehreren Dateien; die Skills brauchen
`Suchanfragen.csv` und `Seiten.csv`.

- Für den **Wochen-Check** vorher oben „Vergleichen" wählen und zwei gleich
  lange Zeiträume einstellen; die Spalten heißen dann z. B. „Klicks
  (Letzte 7 Tage)" und „Klicks (Vorherige 7 Tage)".
- Für **Chancen-Keywords** reicht der Standard-Export über 28 Tage.
- Für die **Kannibalisierung** einer Suchanfrage: Filter „Suchanfrage" auf
  den Begriff setzen, Tab „Seiten" wählen, exportieren. Die Oberfläche
  liefert Anfrage-Seite-Paare nicht in einer Datei; für die ganze Property
  ist der MCP-Weg der praktikable.

**Indexierungsbericht** (für `gsc-indexierungs-check`): Indexierung →
Seiten → „Exportieren" → CSV. Die Tabelle zeigt die Gründe mit Seitenzahl;
die betroffenen URLs stehen im Export des jeweiligen Grundes.

## Google Analytics 4

Für `ga4-zeitraum-vergleich`: Berichte → Akquisition → „Trafficakquisition"
(Kanäle) sowie Interaktion → „Landingpage". Oben den Zeitraum und den
Vergleichszeitraum einstellen, dann rechts oben „Freigeben" → „Datei
herunterladen" → CSV. Beide Zeiträume stehen nebeneinander in der Datei.
Metriken, die der Skill braucht: Sitzungen, Nutzer, Key Events
(Conversions), Interaktionsrate.

## Google Ads

Für `ads-budget-check`: Kampagnen → „Suchkeywords" mit Zeitraum 30 Tage,
Spalten Kosten, Klicks, Impressionen, Conversions, Klickrate → Download-
Symbol → CSV. Dazu „Suchbegriffe" → Download → CSV. Kosten stehen im
Export in Kontowährung. Die Summenzeilen am Ende der Tabelle vor dem
Einfügen löschen oder dem Skill sagen, dass sie da sind.

## Was der CSV-Weg nicht kann

- keine Einzelprüfung von URLs mit letztem Crawl (Indexierung)
- keine Anfrage-Seite-Paare für die ganze Property (Kannibalisierung)
- keine Folgefragen ohne neuen Export
- keine Rechte-Stufen: der Export enthält, was der Nutzer sieht

Für den MCP-Weg brauchst du einen Search-Console-, GA4- oder Ads-MCP, der
mit deinem Google-Konto verbunden ist; das Plugin bringt die Adressen der
gehosteten mcpwerk-Server mit.

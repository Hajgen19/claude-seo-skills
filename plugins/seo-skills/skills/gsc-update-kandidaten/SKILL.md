---
name: gsc-update-kandidaten
description: Findet in der Search Console die Seiten, die über 6 bis 12 Monate am stärksten verloren haben und vorher stark waren – Kandidaten für eine Aktualisierung. Per MCP oder CSV-Export. Nutzen bei Update-Kandidaten, Content-Refresh, Ranking verloren.
license: MIT
compatibility: Benötigt einen verbundenen Google-Search-Console-MCP mit compare_search_periods, get_search_by_page_query und list_properties (entwickelt gegen den gehosteten mcpwerk-Server, Stufe "Lesen" reicht).
allowed-tools: mcp__mcpwerk-gsc__compare_search_periods mcp__mcpwerk-gsc__get_search_by_page_query mcp__mcpwerk-gsc__get_advanced_search_analytics mcp__mcpwerk-gsc__list_properties
metadata:
  author: mcpwerk
  version: "0.1"
  sprache: de
---

# Update-Kandidaten aus der Search Console

Du findest die Seiten, bei denen sich eine Aktualisierung am ehesten
lohnt: Seiten, die vor einigen Monaten eine gute Position, viele
Impressionen und Klicks hatten und seitdem am stärksten verloren haben.
Für jede Kandidatin sagst du, welche Suchanfragen sie verloren hat, damit
klar ist, was der Text wieder beantworten muss. Du liest nur, du änderst
nichts.

## Eingaben

- **Property** (Pflicht), exakt wie in der Search Console; bei Unsicherheit
  `list_properties`.
- **Vergleichshorizont** (optional): Standard 12 Monate — die letzten 28
  Tage gegen dieselben 28 Tage vor einem Jahr. Sagt der Nutzer „6 Monate"
  oder „Quartal", entsprechend anpassen. Die Search Console hält Daten
  begrenzt lange vor; ist der Vorzeitraum leer, sag das und nimm den
  ältesten verfügbaren.
- **Mindeststärke** (optional): Standard: im Vorzeitraum mindestens 100
  Impressionen und Position besser als 10. Bei kleinen Seiten 30
  Impressionen und Position besser als 20, und das sagen.

## Ablauf

1. Setze beide Zeiträume mit Datum (Ende des aktuellen Zeitraums = heute
   minus 3 Tage wegen Nachlauf).
2. Rufe `compare_search_periods` mit `dimensions: page`, Zeitraum 1 =
   Vorzeitraum, Zeitraum 2 = aktueller Zeitraum, `limit: 50` auf.
3. Filtere im Kopf: nur Seiten, die im Vorzeitraum die Mindeststärke
   hatten **und** jetzt weniger Klicks oder eine schlechtere Position
   haben. Sortiere nach verlorenen Klicks, dann nach Positionsverlust.
   Behalte höchstens 10.
4. Für die drei stärksten Kandidatinnen: `get_search_by_page_query` für
   den aktuellen Zeitraum, und `get_advanced_search_analytics` mit
   `dimensions: query`, `filter_dimension: page`, `filter_operator: equals`
   für den Vorzeitraum. Vergleiche: welche Suchanfragen sind weg oder
   deutlich gefallen.
5. Bericht nach der Vorlage.

## Vorlage

**Update-Kandidaten <Property> · <aktueller Zeitraum> gegen <Vorzeitraum> · Datenstand <Datum>**

| Seite | Klicks vorher | Klicks jetzt | Position vorher | Position jetzt | Impressionen vorher |
| --- | --- | --- | --- | --- | --- |
| bis zu 10 Seiten, stärkster Verlust zuerst | | | | | |

Für die Top 3 je ein Absatz: verlorene Suchanfragen (bis zu fünf), was
die Seite heute offenbar nicht mehr beantwortet, und ein Vorschlag, was
beim Update rein muss (neuer Abschnitt, aktualisierte Zahlen, Title,
interne Links). Am Ende ein Satz, welche Seite zuerst dran ist.

## Ohne MCP: mit CSV-Export

Ohne verbundenen MCP arbeitest du mit einem Export, den der Nutzer einfügt
oder hochlädt. Sag dann in einem Satz, dass der Datenstand der Export-
Zeitpunkt ist und jede weitere Frage einen neuen Export braucht; mit MCP
entfällt das. Erkenne Spalten anhand der Überschriften (deutsch oder
englisch), rechne Dezimalkommas und Prozentzeichen um und nenne, welche
Spalten du benutzt hast.

Export für diesen Skill: Search Console → Leistung → „Vergleichen" → die
letzten 28 Tage gegen denselben Zeitraum im Vorjahr (oder 6 Monate zurück)
→ Export → CSV → Datei `Seiten.csv` mit beiden Zeiträumen. Die verlorenen
Suchanfragen je Seite gibt es ohne MCP nur mit einem zweiten Export: Filter
„Seite" auf die URL, Tab „Suchanfragen", Vergleich beibehalten.

## Regeln

- **Verlust braucht eine Basis.** Eine Seite, die vorher fast nichts
  hatte, ist kein Kandidat, auch wenn der Prozentwert dramatisch aussieht.
- **Saisonalität benennen.** Beim 12-Monats-Vergleich fällt Saisonalität
  heraus, beim 6-Monats-Vergleich nicht; sag, welchen du benutzt hast.
- **Neue Seiten trennen.** Seiten ohne Vorzeitraum gehören nicht in die
  Liste.
- **Kein Urteil über Inhalte ohne Blick auf die Seite.** Du schlägst vor,
  was fehlt, anhand der verlorenen Suchanfragen; ob der Text schlecht ist,
  weißt du nicht.
- **Nur lesen.** Keine Änderungen an Sitemaps oder Properties.
- **Deutsch, Du-Form, keine Superlative.**

## Beispiel

Nutzer: "Welche Artikel auf https://beispiel.de/ sollte ich als erstes
aktualisieren?"

Du: vergleichst die letzten 28 Tage mit dem Vorjahr nach Seiten, filterst
auf vorher starke Seiten mit Verlust, holst für die Top 3 die verlorenen
Suchanfragen und gibst Tabelle plus drei Absätze mit Update-Vorschlägen.

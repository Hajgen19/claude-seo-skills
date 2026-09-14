---
name: gsc-wochen-check
description: Search-Console-Check: Zeitraum (Standard 7 Tage) gegen den davor, Gewinner- und Verlierer-Seiten, eine Maßnahme. Nutzen bei Wochen-Check, Wochenreport, Monats-Check, weekly SEO check.
license: MIT
compatibility: Benötigt einen verbundenen Google-Search-Console-MCP mit den Befehlen compare_search_periods, get_performance_overview und list_properties (entwickelt gegen den gehosteten mcpwerk-Server, Stufe "Lesen" reicht).
allowed-tools: mcp__mcpwerk-search-console__compare_search_periods mcp__mcpwerk-search-console__get_performance_overview mcp__mcpwerk-search-console__list_properties
metadata:
  author: mcpwerk
  version: "0.1"
  sprache: de
---

# Wochen-Check für die Google Search Console

Du lieferst denselben kurzen Bericht in immer derselben Form: Was hat sich
in der Google-Suche verändert, welche Seiten gewinnen, welche verlieren, und
was ist die eine Sache, die man jetzt tun sollte. Du holst alle Zahlen
selbst über den Search-Console-MCP. Du schreibst nichts in die Search
Console und änderst nichts.

## Eingaben

- **Property** (Pflicht): exakt so, wie sie in der Search Console heißt,
  z. B. `https://beispiel.de/` (mit Schrägstrich) oder `sc-domain:beispiel.de`.
  Fehlt die Angabe oder ist sie unsicher: zuerst `list_properties` aufrufen
  und die Auswahl bestätigen lassen.
- **Zeitraum** (optional): Standard 7 Tage gegen die 7 Tage davor. Sagt der
  Nutzer "28 Tage", "Monat" oder "Quartal", nimm 28, 30 bzw. 90 Tage gegen
  den gleich langen Zeitraum davor.

## Ablauf

1. **Datenstand festlegen.** Die Search Console liefert Daten mit zwei bis
   drei Tagen Verzögerung. Setze das Ende des aktuellen Zeitraums auf
   "heute minus 3 Tage" und rechne von dort zurück. Nenne beide Zeiträume
   mit Datum im Bericht.
2. **Vergleich nach Seiten.** Rufe `compare_search_periods` mit
   `dimensions: page` auf, Zeitraum 1 = Vorperiode, Zeitraum 2 = aktuelle
   Periode, `limit: 15`.
3. **Vergleich nach Suchanfragen.** Rufe `compare_search_periods` mit
   `dimensions: query` auf, gleiche Zeiträume, `limit: 15`. Nutze das für
   die Einordnung; zeige Suchanfragen nur, wenn der Nutzer sie sehen will.
4. **Gesamtbild.** Rufe `get_performance_overview` mit `days: 28` (bei
   längeren Zeiträumen `days: 90`) auf, damit die Zahlen einen Bezug haben.
5. **Bericht schreiben** nach der Vorlage unten.

## Vorlage für den Bericht

**Check <Property> · <Zeitraum 2> gegen <Zeitraum 1> · Datenstand <Datum>**

| Seite | Klicks vorher | Klicks jetzt | Position vorher | Position jetzt |
| --- | --- | --- | --- | --- |
| Gewinner 1–3 | | | | |
| Verlierer 1–3 | | | | |

Drei Sätze Einordnung: (1) Gesamtrichtung in Klicks und Impressionen,
(2) die auffälligste Bewegung mit möglicher Ursache, (3) was unauffällig
geblieben ist.

**Eine Maßnahme**, konkret: welche Seite, was genau (Title prüfen, internen
Link setzen, Inhalt aktualisieren, abwarten).

## Regeln

- **Kleine Zahlen sind Richtungen, keine Prozente.** Unter 20 Klicks pro
  Zeitraum keine Prozentangaben; schreibe "von 3 auf 7 Klicks".
- **Neue Seiten kennzeichnen.** Eine Seite ohne Werte in Zeitraum 1 ist
  neu, kein Gewinner mit "+100 %".
- **Rauschen benennen.** Bewegungen von ein bis zwei Klicks nicht deuten.
- **Nur lesen.** Dieser Skill ruft keine Befehle auf, die Sitemaps oder
  Properties verändern. Will der Nutzer eine Änderung, sag, dass das ein
  anderer Schritt ist, und frag nach.
- **Ehrlich bei leerer Antwort.** Kommt nichts zurück, ist meist der
  Property-Name falsch (Schrägstrich, `sc-domain:`). Zeige die Liste aus
  `list_properties` und frag nach.
- **Deutsch, Du-Form, keine Superlative.**

## Beispiel

Nutzer: "Mach den Wochen-Check für https://beispiel.de/"

Du: rufst die drei Befehle auf, gibst die Tabelle mit je bis zu drei
Gewinnern und Verlierern aus, drei Sätze Einordnung, eine Maßnahme, und
nennst den Datenstand.

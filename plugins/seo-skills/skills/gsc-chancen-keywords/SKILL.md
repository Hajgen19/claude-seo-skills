---
name: gsc-chancen-keywords
description: Findet Chancen-Keywords in der Search Console: viele Impressionen, Position 8 bis 20, schwache Klickrate, mit Title-Empfehlung. Nutzen bei Chancen-Keywords, Striking Distance, Seite 2.
license: MIT
compatibility: Benötigt einen verbundenen Google-Search-Console-MCP mit get_advanced_search_analytics und get_search_by_page_query (entwickelt gegen den gehosteten mcpwerk-Server, Stufe "Lesen" reicht).
allowed-tools: mcp__mcpwerk-search-console__get_advanced_search_analytics mcp__mcpwerk-search-console__get_search_by_page_query mcp__mcpwerk-search-console__list_properties
metadata:
  author: mcpwerk
  version: "0.1"
  sprache: de
---

# Chancen-Keywords aus der Search Console

Du findest die Suchanfragen, bei denen eine Website schon sichtbar ist,
aber noch kaum Klicks bekommt: viele Impressionen, Position knapp hinter
der ersten Seite, Klickrate unter der Erwartung. Das sind die Stellen, an
denen ein besserer Title oder ein klareres Snippet am schnellsten wirkt.
Du liest nur, du änderst nichts.

## Eingaben

- **Property** (Pflicht), exakt wie in der Search Console; bei Unsicherheit
  `list_properties`.
- **Zeitraum** (optional): Standard 28 Tage bis "heute minus 3 Tage".
- **Schwellen** (optional): Standard Position 8 bis 20, Klickrate unter 3 %,
  mindestens 20 Impressionen. Bei kleinen Seiten die Impressions-Schwelle
  auf 10 senken und das sagen.

## Ablauf

1. Rufe `get_advanced_search_analytics` auf: `dimensions: query`,
   `sort_by: impressions`, `sort_direction: descending`, `row_limit: 500`,
   Zeitraum wie oben.
2. Filtere im Kopf: Position zwischen 8 und 20, Klickrate unter der
   Schwelle, Impressionen über der Schwelle. Behalte höchstens 15 Zeilen.
3. Für die drei stärksten Kandidaten: `get_search_by_page_query` oder
   `get_advanced_search_analytics` mit `dimensions: query,page` und
   `filter_dimension: query`, `filter_operator: equals`, um die rankende
   URL zu finden.
4. Bericht nach der Vorlage.

## Vorlage

**Chancen-Keywords <Property> · letzte <n> Tage · Datenstand <Datum>**

| Suchanfrage | Impressionen | Klicks | Klickrate | Position | Rankende URL |
| --- | --- | --- | --- | --- | --- |

Für die drei stärksten: je eine Empfehlung in einem Satz, was am Title
oder Snippet der rankenden URL fehlt, damit die Anfrage klickt (Begriff
nicht im Title, kein Nutzenversprechen, Snippet passt nicht zur Frage).

## Regeln

- Nenne die Schwellen, die du benutzt hast.
- Position ist ein Durchschnitt; bei wenigen Impressionen schwankt sie
  stark. Unter 20 Impressionen keine Empfehlung aussprechen, nur listen.
- Branded Suchanfragen (Markenname der Website) getrennt ausweisen.
- Nur lesen. Keine Änderungen an Sitemaps oder Properties.
- Deutsch, Du-Form, keine Superlative.

## Beispiel

Nutzer: "Welche Chancen-Keywords hat https://beispiel.de/ gerade?"

Du: holst die Suchanfragen der letzten 28 Tage, filterst nach den
Schwellen, ermittelst für die Top 3 die rankende URL und gibst Tabelle plus
drei Empfehlungen aus.

---
name: gsc-kannibalisierung
description: Prüft Keyword-Kannibalisierung in der Search Console: Suchanfragen mit zwei oder mehr eigenen URLs, Schweregrad, Empfehlung – per MCP oder CSV-Export. Nutzen bei Kannibalisierung, cannibalization.
license: MIT
compatibility: Benötigt einen verbundenen Google-Search-Console-MCP mit get_advanced_search_analytics (entwickelt gegen den gehosteten mcpwerk-Server, Stufe "Lesen" reicht).
allowed-tools: mcp__mcpwerk-gsc__get_advanced_search_analytics mcp__mcpwerk-gsc__list_properties
metadata:
  author: mcpwerk
  version: "0.1"
  sprache: de
---

# Kannibalisierungs-Check mit der Search Console

Du findest Suchanfragen, für die Google mehrere Seiten derselben Website
ausspielt, und sagst, ob das ein Problem ist. Zwei URLs für eine Anfrage
sind erst dann ein Konflikt, wenn sich Impressionen und Klicks spürbar
aufteilen oder die falsche Seite vorn liegt. Du liest nur, du änderst
nichts.

## Eingaben

- **Property** (Pflicht), exakt wie in der Search Console; bei Unsicherheit
  `list_properties`.
- **Zeitraum** (optional): Standard 90 Tage bis "heute minus 3 Tage".
- **Fokus** (optional): ein Keyword oder ein URL-Pfad. Ohne Fokus prüfst du
  die Property komplett.

## Ablauf

1. Rufe `get_advanced_search_analytics` auf: `dimensions: query,page`,
   `sort_by: impressions`, `row_limit: 2000`, Zeitraum wie oben. Bei Fokus
   zusätzlich `filter_dimension: query` oder `page`, `filter_operator: contains`.
2. Gruppiere im Kopf nach Suchanfrage. Behalte Anfragen mit zwei oder mehr
   URLs, die zusammen mindestens 20 Impressionen haben.
3. Bewerte je Anfrage den Schweregrad:
   - **hoch**: zwei URLs mit je über 30 % der Impressionen, oder die
     URL mit weniger Klicks steht vorn
   - **mittel**: Nebenseite hat 10 bis 30 % der Impressionen
   - **niedrig**: Nebenseite unter 10 % oder unter 10 Impressionen
4. Gib für jede Anfrage mit Schweregrad hoch oder mittel eine Empfehlung:
   zusammenlegen, eine Seite schärfen und die andere umleiten, interne
   Links auf die gewünschte Seite lenken, oder bewusst beide lassen (wenn
   die Intents verschieden sind, z. B. Produktseite und Anleitung).
5. Bericht nach der Vorlage.

## Vorlage

**Kannibalisierung <Property> · <Zeitraum> · Datenstand <Datum>**

| Suchanfrage | URL 1 (Impr./Klicks/Pos.) | URL 2 (Impr./Klicks/Pos.) | Schweregrad | Empfehlung |
| --- | --- | --- | --- | --- |

Darunter zwei Sätze: wie viele Anfragen geprüft wurden und ob es ein
Muster gibt (z. B. Übersichtsseite gegen Detailseite, Doku gegen
Produktseite).

## Ohne MCP: mit CSV-Export

Ohne verbundenen MCP arbeitest du mit einem Export, den der Nutzer einfügt
oder hochlädt. Sag dann in einem Satz, dass der Datenstand der Export-
Zeitpunkt ist und jede weitere Frage einen neuen Export braucht; mit MCP
entfällt das. Erkenne Spalten anhand der Überschriften (deutsch oder
englisch), rechne Dezimalkommas und Prozentzeichen um und nenne, welche
Spalten du benutzt hast.

Die Oberfläche exportiert Suchanfragen und Seiten getrennt, nicht als
Paar. Ohne MCP prüfst du deshalb **eine Suchanfrage je Durchgang**:
Search Console → Leistung → Filter „Suchanfrage" auf den Begriff → Tab
„Seiten" → Export → CSV. Die Datei zeigt alle URLs, die für diese Anfrage
ranken, mit Klicks, Impressionen, Position. Für eine ganze Property ist
der MCP-Weg der einzige praktikable; sag das offen.

## Regeln

- Kein Konflikt ohne Zahlen: nenne immer Impressionen und Klicks beider
  URLs.
- Unterschiedliche Intents sind keine Kannibalisierung. Sag das
  ausdrücklich, wenn es zutrifft.
- Bei Properties mit sehr wenigen Impressionen ist die Aussagekraft
  gering; sag das statt Empfehlungen zu erfinden.
- Nur lesen. Keine Änderungen an Sitemaps oder Properties.
- Deutsch, Du-Form, keine Superlative.

## Beispiel

Nutzer: "Kannibalisieren sich bei https://beispiel.de/ Seiten für 'search console mcp'?"

Du: holst Anfrage-Seite-Paare der letzten 90 Tage mit Filter auf die
Anfrage, listest die beteiligten URLs mit Zahlen, bewertest den
Schweregrad und gibst eine Empfehlung.

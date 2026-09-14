---
name: gsc-indexierungs-check
description: Prüft den Indexierungsstatus von URLs in der Search Console und nennt nicht indexierte Seiten mit Grund – per MCP oder CSV-Export. Nutzen bei Indexierung prüfen, nach Deploy.
license: MIT
compatibility: Benötigt einen verbundenen Google-Search-Console-MCP mit check_indexing_issues, batch_url_inspection und list_sitemaps_enhanced (entwickelt gegen den gehosteten mcpwerk-Server, Stufe "Lesen" reicht).
allowed-tools: mcp__mcpwerk-gsc__check_indexing_issues mcp__mcpwerk-gsc__batch_url_inspection mcp__mcpwerk-gsc__list_sitemaps_enhanced mcp__mcpwerk-gsc__list_properties
metadata:
  author: mcpwerk
  version: "0.1"
  sprache: de
---

# Indexierungs-Check mit der Search Console

Du prüfst für eine Liste von URLs, ob Google sie kennt und indexiert hat,
und nennst bei jeder nicht indexierten Seite den Grund, den die Search
Console meldet. Du liest nur; du reichst keine Sitemaps ein und änderst
nichts.

## Eingaben

- **Property** (Pflicht), exakt wie in der Search Console; bei Unsicherheit
  `list_properties`. Für Domain-Properties gilt das Format
  `sc-domain:beispiel.de`.
- **URLs** (eine der drei Varianten):
  1. eine Liste, die der Nutzer nennt oder einfügt,
  2. "alle Seiten der Sitemap": dann `list_sitemaps_enhanced` aufrufen, die
     Sitemap-URLs nennen und den Nutzer die URL-Liste bestätigen lassen,
     bevor du prüfst (die Sitemap selbst liest der MCP nicht aus),
  3. "die wichtigsten Seiten": dann die Startseite plus die URLs, die der
     Nutzer als wichtig bezeichnet.

## Ablauf

1. Prüfe höchstens 20 URLs je Aufruf. Bei längeren Listen in Blöcken
   arbeiten und das sagen; jede Prüfung zählt auf ein Tageskontingent bei
   Google.
2. Rufe `check_indexing_issues` mit der Property und der URL-Liste auf
   (eine URL je Zeile).
3. Für URLs, die als nicht indexiert oder mit Abrufproblem gemeldet werden,
   hole mit `batch_url_inspection` die Details (letzter Crawl, Canonical,
   robots-Status).
4. Bericht nach der Vorlage.

## Vorlage

**Indexierungs-Check <Property> · <n> URLs · Datenstand <Datum>**

Zusammenfassung: indexiert / nicht indexiert / Canonical-Abweichung /
robots-Sperre / Abrufproblem.

| URL | Status | Grund laut Search Console | Letzter Crawl | Empfehlung |
| --- | --- | --- | --- | --- |

Empfehlung je Fall in einem Satz: "URL ist Google unbekannt" heißt meist
fehlende interne Verlinkung oder Sitemap-Eintrag; "gecrawlt, nicht
indexiert" heißt Inhalt oder Duplikat prüfen; Canonical auf eine andere URL
ist oft gewollt; robots-Sperre gegen die Absicht abgleichen.

## Ohne MCP: mit CSV-Export

Ohne verbundenen MCP arbeitest du mit einem Export, den der Nutzer einfügt
oder hochlädt. Sag dann in einem Satz, dass der Datenstand der Export-
Zeitpunkt ist und jede weitere Frage einen neuen Export braucht; mit MCP
entfällt das. Erkenne Spalten anhand der Überschriften (deutsch oder
englisch), rechne Dezimalkommas und Prozentzeichen um und nenne, welche
Spalten du benutzt hast.

Export für diesen Skill: Search Console → Indexierung → Seiten → Export →
CSV. Die Tabelle listet je Grund („Gecrawlt – zurzeit nicht indexiert",
„Duplikat …", „Durch robots.txt blockiert" …) die Anzahl der Seiten; die
betroffenen URLs stehen in den Detail-Exporten je Grund. Ordne die
Gründe nach Anzahl, erkläre je Grund in einem Satz, was er bedeutet, und
gib die Empfehlung aus der Vorlage. Eine Einzelprüfung mit letztem Crawl
gibt es ohne MCP nicht.

## Regeln

- Eine bewusst auf noindex gesetzte Seite (Landingpages für Anzeigen,
  Portal, Rechtsseiten) ist kein Fehler; frag nach, wenn der Zweck unklar
  ist.
- Kein Einreichen, kein Löschen, keine Änderung an Sitemaps.
- Bei Fehlern des Befehls (z. B. Property nicht gefunden) den exakten
  Property-Namen prüfen, nicht raten.
- Deutsch, Du-Form, keine Superlative.

## Beispiel

Nutzer: "Prüf, ob unsere vier Produktseiten und die zwei neuen Artikel
indexiert sind."

Du: lässt dir die sechs URLs geben oder leitest sie aus dem Gespräch ab,
rufst `check_indexing_issues` auf, holst bei Auffälligkeiten Details und
gibst die Tabelle mit Empfehlungen aus.

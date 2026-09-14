---
name: ga4-anomalie-check
description: Prüft in GA4, ob Ereignisse oder Key Events plötzlich ausbleiben oder einbrechen – Hinweis auf kaputtes Tracking, per MCP oder CSV-Export. Nutzen bei Anomalie-Check, Tracking prüfen.
license: MIT
compatibility: Benötigt einen verbundenen Google-Analytics-4-MCP mit get_ga4_data, batch_run_reports und list_accounts_and_properties (entwickelt gegen den gehosteten mcpwerk-Server, Stufe "Lesen" reicht).
allowed-tools: mcp__mcpwerk-ga4__get_ga4_data mcp__mcpwerk-ga4__batch_run_reports mcp__mcpwerk-ga4__list_accounts_and_properties
metadata:
  author: mcpwerk
  version: "0.1"
  sprache: de
---

# Anomalie-Check für Google Analytics 4

Du findest Ereignisse, die auf einmal fehlen oder einbrechen: ein Key
Event, das vorher täglich kam und seit drei Tagen bei null steht, ein
Formular-Event, das nach einem Website-Update verschwunden ist, ein
Kanal, der über Nacht weg ist. Das ist fast immer ein Tracking-Problem,
kein Nutzerverhalten, und es muss schnell auffallen. Du liest nur, du
änderst nichts an der Property.

## Eingaben

- **Property-ID** (Pflicht, Zahl). Fehlt sie: `list_accounts_and_properties`
  aufrufen und bestätigen lassen.
- **Prüffenster** (optional): Standard die letzten 3 Tage gegen den
  Tagesdurchschnitt der 28 Tage davor. Bei sehr kleinen Properties 7 Tage
  gegen 56 Tage, und das sagen.
- **Fokus** (optional): bestimmte Ereignisse (z. B. `generate_lead`,
  `purchase`, `sign_up`) oder alle.

## Ablauf

1. Rufe `get_ga4_data` mit `dimensions: ["date", "eventName"]`,
   `metrics: ["eventCount"]`, `date_range_start: "31daysAgo"`,
   `date_range_end: "yesterday"`, `limit: 2000` auf. Bei der Warnung
   „large dataset" die Ereignisse per `dimension_filter` auf die wichtigen
   einschränken statt `proceed_with_large_dataset` zu setzen.
2. Rufe zusätzlich `get_ga4_data` mit `dimensions: ["date"]`,
   `metrics: ["sessions", "keyEvents"]`, gleicher Zeitraum, auf — das
   Gesamtbild, damit ein Einbruch eines Ereignisses gegen einen Einbruch
   des Traffics unterschieden werden kann.
3. Berechne im Kopf je Ereignis den Tagesdurchschnitt der Basisperiode
   (Tage 4 bis 31 zurück) und die Summe des Prüffensters (letzte 3 Tage).
   Beachte: GA4 liefert den gestrigen Tag oft unvollständig; werte den
   letzten Tag nur als Tendenz.
4. Markiere als **Anomalie**:
   - Ereignis mit Basis ≥ 5 pro Tag, im Prüffenster **null** → „ausgefallen"
   - Ereignis mit Basis ≥ 5 pro Tag, im Prüffenster unter 30 % des
     Erwarteten → „eingebrochen"
   - Ereignis, das in der Basis fehlte und jetzt täglich kommt → „neu"
     (kann gewollt sein)
   - Sitzungen stabil, aber Key Events auf null → „Tracking-Verdacht"
5. Bericht nach der Vorlage.

## Vorlage

**Anomalie-Check Property <ID> · Prüffenster <Daten> gegen Basis <Daten>**

| Ereignis | Basis pro Tag | Erwartet im Fenster | Tatsächlich | Befund |
| --- | --- | --- | --- | --- |

Darunter: ein Satz zum Gesamtbild (Sitzungen und Key Events im Fenster
gegen Basis), dann je Anomalie ein Satz mit der wahrscheinlichsten
Ursache (Tag-Manager-Änderung, Website-Update, Consent, Kampagnenende) und
dem ersten Prüfschritt.

## Ohne MCP: mit CSV-Export

Ohne verbundenen MCP arbeitest du mit einem Export, den der Nutzer einfügt
oder hochlädt. Sag dann in einem Satz, dass der Datenstand der Export-
Zeitpunkt ist und jede weitere Frage einen neuen Export braucht; mit MCP
entfällt das. Erkenne Spalten anhand der Überschriften (deutsch oder
englisch), rechne Dezimalkommas und Prozentzeichen um und nenne, welche
Spalten du benutzt hast.

Export für diesen Skill: GA4 → Berichte → Interaktion → „Ereignisse", Zeitraum
letzte 31 Tage, oben die sekundäre Dimension „Datum" hinzufügen → „Freigeben"
→ „Datei herunterladen" → CSV. Für das Gesamtbild zusätzlich Berichte →
„Berichtsübersicht" oder Akquisition → „Trafficakquisition" mit Datum.
Regelmäßige Prüfung ohne MCP heißt: jeden Tag ein neuer Export — genau die
Stelle, an der der MCP-Weg den Unterschied macht.

## Regeln

- **Kleine Zahlen sind keine Anomalie.** Ereignisse mit Basis unter 5 pro
  Tag nur listen, nicht bewerten.
- **Traffic-Einbruch ist keine Tracking-Anomalie.** Fallen Sitzungen und
  Ereignisse gemeinsam, sag das und verweise auf den Zeitraum-Vergleich.
- **Wochenende und Feiertage** benennen, wenn das Prüffenster darauf fällt.
- **Nur lesen.** Keine Änderungen an Key Events, Streams oder Dimensionen.
- **Deutsch, Du-Form, keine Superlative.**

## Beispiel

Nutzer: "Mach den Anomalie-Check für Property 123456789."

Du: holst Ereignisse je Tag für 31 Tage plus Sitzungen und Key Events je
Tag, vergleichst die letzten 3 Tage mit dem Tagesdurchschnitt davor,
listest ausgefallene und eingebrochene Ereignisse mit Zahlen und nennst je
Anomalie einen Prüfschritt.

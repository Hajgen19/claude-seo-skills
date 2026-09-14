---
name: ga4-zeitraum-vergleich
description: Vergleicht in GA4 zwei frei wählbare Zeiträume nach Kanal und Landingpage, nennt Gewinner, Verlierer, Potenziale – per MCP oder CSV-Export. Nutzen bei GA4-Vergleich, wo wächst der Traffic.
license: MIT
compatibility: Benötigt einen verbundenen Google-Analytics-4-MCP mit batch_run_reports, get_ga4_data und list_accounts_and_properties (entwickelt gegen den gehosteten mcpwerk-Server, Stufe "Lesen" reicht).
allowed-tools: mcp__mcpwerk-ga4__batch_run_reports mcp__mcpwerk-ga4__get_ga4_data mcp__mcpwerk-ga4__list_accounts_and_properties
metadata:
  author: mcpwerk
  version: "0.1"
  sprache: de
---

# Zeitraum-Vergleich in Google Analytics 4

Du vergleichst zwei Zeiträume einer GA4-Property und sagst, wo der Traffic
herkommt, wo er wächst, wo er fällt und wo ein Potenzial liegt, das man
sonst übersieht: ein neuer Kanal, eine Landingpage mit Nutzern ohne
Conversions, eine Seite, die von selbst anzieht. Du liest nur, du
änderst nichts an der Property.

## Eingaben

- **Property-ID** (Pflicht, Zahl). Fehlt sie: `list_accounts_and_properties`
  aufrufen und die Auswahl bestätigen lassen.
- **Zeitraum** (optional): Standard 28 Tage gegen die 28 Tage davor. Der
  Nutzer kann "letzter Monat gegen Vormonat", "Quartal", "90 Tage" oder
  "gleicher Zeitraum Vorjahr" sagen. Relative Angaben sind erlaubt
  (`28daysAgo`, `yesterday`); GA4 liefert bis gestern belastbar.
- **Fokus** (optional): Kanäle, Landingpages oder beides (Standard).

## Ablauf

1. Baue die beiden Zeiträume mit Datum und nenne sie im Bericht.
2. Rufe `batch_run_reports` mit bis zu vier Berichten auf:
   - Kanäle, aktueller Zeitraum: `dimensions: ["sessionDefaultChannelGroup"]`,
     `metrics: ["sessions", "totalUsers", "keyEvents", "engagementRate"]`
   - Kanäle, Vorzeitraum: gleiche Felder, andere Daten
   - Landingpages, aktueller Zeitraum: `dimensions: ["landingPage"]`,
     `metrics: ["sessions", "totalUsers", "keyEvents"]`, `limit: 25`
   - Landingpages, Vorzeitraum: gleiche Felder
3. Rechne im Kopf die Differenzen je Kanal und je Landingpage.
4. Suche Potenziale:
   - ein Kanal, der im Vorzeitraum fehlte und jetzt Sitzungen bringt
     (z. B. "AI Assistant", "Referral" von einer neuen Quelle)
   - Landingpages mit steigenden Nutzern und null Key Events
   - Landingpages, die neu in der Liste sind, ohne dass jemand sie beworben
     hat
   - ein Kanal mit hoher Engagement-Rate, aber wenig Sitzungen
5. Bericht nach der Vorlage.

## Vorlage

**GA4-Vergleich Property <ID> · <Zeitraum 2> gegen <Zeitraum 1>**

| Kanal | Sitzungen vorher | Sitzungen jetzt | Key Events jetzt | Engagement-Rate jetzt |
| --- | --- | --- | --- | --- |

| Landingpage | Nutzer vorher | Nutzer jetzt | Key Events jetzt |
| --- | --- | --- | --- |
| Top 5 Gewinner, Top 3 Verlierer | | | |

**Potenziale** (bis zu drei, je ein Satz mit Zahl und Vorschlag).

Zwei Sätze Einordnung zur Gesamtrichtung.

## Ohne MCP: mit CSV-Export

Ohne verbundenen MCP arbeitest du mit einem Export, den der Nutzer einfügt
oder hochlädt. Sag dann in einem Satz, dass der Datenstand der Export-
Zeitpunkt ist und jede weitere Frage einen neuen Export braucht; mit MCP
entfällt das. Erkenne Spalten anhand der Überschriften (deutsch oder
englisch), rechne Dezimalkommas und Prozentzeichen um und nenne, welche
Spalten du benutzt hast.

Export für diesen Skill: GA4 → Berichte → Akquisition → „Trafficakquisition"
(Kanäle) und Interaktion → „Landingpage", jeweils mit eingestelltem
Vergleichszeitraum, dann „Freigeben" → „Datei herunterladen" → CSV. Die
Datei enthält beide Zeiträume nebeneinander. Fehlt der Vergleich, bitte um
je einen Export pro Zeitraum. Metriken: Sitzungen, Nutzer, Key Events
(Conversions), Interaktionsrate.

## Regeln

- Bei Sitzungen unter 50 je Zeitraum keine Prozentangaben, nur absolute
  Zahlen und Richtungen.
- `(not set)` als Landingpage ist ein Messartefakt, nicht deuten.
- Key Events heißen in älteren Oberflächen Conversions; nenne sie so, wie
  die Property sie liefert.
- Kein Vergleich ohne gleich lange Zeiträume.
- Nur lesen. Keine Änderungen an Key Events, Streams oder Dimensionen.
- Deutsch, Du-Form, keine Superlative.

## Beispiel

Nutzer: "Vergleich in GA4 den letzten Monat mit dem Vormonat für Property
123456789 und sag mir, wo Potenzial liegt."

Du: setzt die Zeiträume, holst die vier Berichte, gibst beide Tabellen aus
und nennst bis zu drei Potenziale mit Zahl.

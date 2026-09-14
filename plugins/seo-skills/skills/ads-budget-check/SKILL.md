---
name: ads-budget-check
description: Prüft in Google Ads, wohin das Budget floss: Keywords und Suchbegriffe mit Kosten ohne Conversions, teure Klicks, Negativ-Vorschläge. Nutzen bei Budget-Check, wasted spend, was kostet nur.
license: MIT
compatibility: Benötigt einen verbundenen Google-Ads-MCP mit run_gaql und list_accounts (entwickelt gegen den gehosteten mcpwerk-Server, Stufe "Lesen" bzw. "Analyse & Keyword-Recherche" reicht).
allowed-tools: mcp__mcpwerk-google-ads__run_gaql mcp__mcpwerk-google-ads__list_accounts
metadata:
  author: mcpwerk
  version: "0.1"
  sprache: de
---

# Budget-Check für Google Ads

Du zeigst, welche Keywords und Suchbegriffe im Zeitraum Geld gekostet, aber
nichts gebracht haben, und was man daraus macht. Du liest nur. Du legst
keine Negativ-Keywords an, pausierst nichts und änderst keine Gebote; du
schlägst vor.

## Eingaben

- **Konto** (Pflicht): die Kundennummer (10 Ziffern, ohne Bindestriche).
  Liegt das Konto unter einem Verwaltungskonto (MCC), brauchst du beide
  Nummern: `customer_id` und `manager_id`. Fehlt etwas: `list_accounts`
  aufrufen und bestätigen lassen.
- **Zeitraum** (optional): Standard `LAST_30_DAYS`; erlaubt sind auch
  `LAST_7_DAYS`, `LAST_MONTH` oder ein Datumsbereich `'YYYY-MM-DD' AND 'YYYY-MM-DD'`.
- **Schwelle** (optional): Standard "Kosten über 0 und Conversions gleich 0".

## Ablauf

1. **Keywords ohne Conversion.** Rufe `run_gaql` auf:

   ```
   SELECT campaign.name, ad_group.name, ad_group_criterion.keyword.text,
          ad_group_criterion.keyword.match_type, metrics.impressions,
          metrics.clicks, metrics.cost_micros, metrics.conversions, metrics.ctr
   FROM keyword_view
   WHERE segments.date DURING LAST_30_DAYS
     AND campaign.status = 'ENABLED'
     AND metrics.cost_micros > 0
   ORDER BY metrics.cost_micros DESC
   LIMIT 50
   ```

2. **Suchbegriffe, die Klicks gekostet haben.** Rufe `run_gaql` auf:

   ```
   SELECT search_term_view.search_term, campaign.name, ad_group.name,
          metrics.clicks, metrics.cost_micros, metrics.conversions
   FROM search_term_view
   WHERE segments.date DURING LAST_30_DAYS AND metrics.clicks > 0
   ORDER BY metrics.cost_micros DESC
   LIMIT 50
   ```

3. Rechne Kosten um: `cost_micros` geteilt durch 1.000.000 ergibt den
   Betrag in der Kontowährung. Ohne Umrechnung keine Zahl ausgeben.
4. Ordne: Keywords und Suchbegriffe mit Kosten und null Conversions,
   sortiert nach Kosten; darunter die teuersten Klicks (Kosten je Klick)
   und Keywords mit Klickrate unter 2 % bei mehr als 100 Impressionen.
5. Bericht nach der Vorlage.

## Vorlage

**Budget-Check Konto <Nummer> · <Zeitraum>**

Gesamt: Kosten, Klicks, Conversions, Kosten je Klick.

| Keyword / Suchbegriff | Kampagne | Impressionen | Klicks | Kosten | Conversions |
| --- | --- | --- | --- | --- | --- |

**Vorschläge** (bis zu fünf, je ein Satz): Suchbegriffe, die als
Negativ-Keyword sinnvoll wären; Keywords, deren Übereinstimmungstyp zu
weit ist; Keywords, die man beobachten sollte, weil die Zahlen noch klein
sind.

## Regeln

- **Kleine Konten, kleine Zahlen.** Unter 10 Klicks je Keyword sind null
  Conversions kein Urteil. Sag das und empfiehl Beobachten statt Streichen.
- **Conversion-Tracking prüfen.** Zeigt das ganze Konto null Conversions,
  ist vermutlich das Tracking nicht eingerichtet; dann keine
  Streich-Empfehlungen, sondern zuerst diesen Hinweis.
- **Micros immer umrechnen** und die Währung des Kontos nennen.
- **Nur lesen.** Keine Befehle, die Keywords, Gebote oder Kampagnen ändern.
  Will der Nutzer Negativ-Keywords anlegen, ist das ein eigener Schritt mit
  ausdrücklicher Bestätigung.
- **Gelöschte Kampagnen ausblenden** über `campaign.status = 'ENABLED'`.
- Deutsch, Du-Form, keine Superlative.

## Beispiel

Nutzer: "Mach den Budget-Check für Konto 1234567890 unter MCC 9876543210."

Du: führst beide Abfragen aus, rechnest Micros um, listest Keywords und
Suchbegriffe ohne Conversion und gibst bis zu fünf Vorschläge.

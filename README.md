# Claude SEO Skills

Sieben SEO-Skills für Claude als installierbares Plugin. Sechs davon sind
wiederkehrende Auswertungen und Audits mit festem Ablauf: vier für die
Google Search Console, einer für Google Analytics 4, einer für Google Ads.
Der siebte ist die vollständige Keyword-Recherche `seo-page-research` für
Claude Code. Alle Skills nutzen nur lesende Befehle. Deutsch, Du-Form, MIT.

**Skills legen den Ablauf fest. Die Daten kommen live per MCP** von einem
Server, der mit deinem Google-Konto verbunden ist. Wer noch keinen MCP hat,
kann die sechs Routine-Skills auch mit **CSV-Exporten** aus der
Google-Oberfläche nutzen; dann ist der Datenstand der Export-Zeitpunkt und
jede weitere Frage braucht einen neuen Export. Anleitung je Produkt:
[`plugins/seo-skills/CSV-EXPORTE.md`](plugins/seo-skills/CSV-EXPORTE.md).

Entwickelt und getestet gegen die gehosteten MCP-Server von
[mcpwerk](https://mcpwerk.com); die Befehlsnamen in den Skills sind die von
mcpwerk. Mit einem anderen Search-Console-, GA4- oder Ads-MCP passt du die
Befehlsnamen in der jeweiligen `SKILL.md` an.

## Die Skills

| Skill | Was er tut | Rhythmus | Daten |
| --- | --- | --- | --- |
| `gsc-wochen-check` | Zeitraum gegen Vorzeitraum (Standard 7 Tage, auch 28 oder 90), Gewinner- und Verlierer-Seiten, eine Maßnahme | wöchentlich | Search Console, MCP oder CSV |
| `gsc-chancen-keywords` | Suchanfragen mit vielen Impressionen auf Position 8–20 und schwacher Klickrate, mit Title-Empfehlung | monatlich | Search Console, MCP oder CSV |
| `gsc-kannibalisierung` | Suchanfragen, für die zwei oder mehr eigene URLs ranken, mit Schweregrad und Empfehlung | vor neuen Seiten | Search Console, MCP (CSV nur je Anfrage) |
| `gsc-indexierungs-check` | URL-Liste gegen Indexierungsstatus, nicht indexierte Seiten mit Grund | nach Deploys | Search Console, MCP oder Export des Seiten-Berichts |
| `ga4-zeitraum-vergleich` | Zwei frei wählbare Zeiträume nach Kanal und Landingpage, Gewinner, Verlierer, Potenziale | monatlich | GA4, MCP oder CSV |
| `ads-budget-check` | Keywords und Suchbegriffe mit Kosten ohne Conversions, teure Klicks, Negativ-Vorschläge | wöchentlich | Google Ads, MCP oder CSV |
| `seo-page-research` | Vollständige SERP-first-Keywordrecherche für eine Seite in sechs Phasen bis zum Content-Briefing | je neue Seite | **nur Claude Code**; SerpApi-Key nötig, Planner und Search Console per MCP |

`seo-page-research` ist bewusst anders als die sechs anderen: mehrphasig,
mit Bestätigungsschritten, schreibt Dateien in deinen Projektordner und
braucht einen eigenen SerpApi-Account (MCP-Eintrag `serpapi` in der
`.mcp.json` deines Projekts). Er läuft nicht in claude.ai. Details in
[`plugins/seo-skills/skills/seo-page-research/README.md`](plugins/seo-skills/skills/seo-page-research/README.md).

## Voraussetzungen

1. Ein Claude-Konto mit aktivierter Codeausführung (Skills laufen laut
   Anthropic-Hilfe auf Free, Pro, Max, Team und Enterprise).
2. Für den MCP-Weg: ein verbundener MCP-Server je Google-Produkt. Das Plugin
   bringt eine `.mcp.json` mit den mcpwerk-Adressen mit
   (`https://mcp.mcpwerk.com/gsc/mcp`, `/ga4/mcp`, `/ads/mcp`; Servernamen
   `mcpwerk-gsc`, `mcpwerk-ga4`, `mcpwerk-ads`). Für die Anmeldung brauchst
   du ein mcpwerk-Konto mit verbundenem Google-Konto; die Stufe „Lesen"
   reicht für alle Routine-Skills.
3. Für den CSV-Weg: nichts außer dem Export.

## Installation

**Claude Code** (Terminal):

```
/plugin marketplace add Hajgen19/claude-seo-skills
/plugin install seo-skills@claude-seo-skills
```

Danach heißen die Skills `/seo-skills:gsc-wochen-check` usw. Beim ersten
Aufruf eines MCP-Servers meldest du dich einmal im Browser an.

**Claude Desktop** (ohne Terminal): Anpassen → Persönliche Plugins →
Marketplace hinzufügen → `Hajgen19/claude-seo-skills` → Plugin `seo-skills`
installieren.

**claude.ai im Browser**: einzelnen Skill-Ordner aus `plugins/seo-skills/skills/`
als ZIP packen (der Skill-Ordner ist das Root-Element) und unter
Anpassen → Skills hochladen. Den MCP-Server verbindest du dort als
benutzerdefinierten Connector mit derselben Adresse, oder du nutzt den
CSV-Weg.

**Lokal testen** (Claude Code):

```
claude --plugin-dir ./plugins/seo-skills
```

## Beispiel

```
Mach den Wochen-Check für https://beispiel.de/
```

Claude ruft `compare_search_periods` und `get_performance_overview` auf,
berücksichtigt den Nachlauf der Search Console, gibt eine Tabelle mit bis zu
drei Gewinnern und Verlierern aus, drei Sätze Einordnung und eine Maßnahme.
Ohne MCP: Export aus dem Leistungsbericht mit Zeitraumvergleich einfügen und
denselben Satz schreiben.

## Eigene Skills bauen

Jeder Skill ist ein Ordner mit einer `SKILL.md`: Frontmatter mit `name` und
`description` (die Beschreibung entscheidet, wann Claude den Skill auslöst;
in claude.ai höchstens 200 Zeichen), danach Eingaben, Ablauf mit den
MCP-Befehlen beim Namen, Ausgabe-Vorlage, Regeln, ein Beispiel. Kopiere den
kürzesten Skill (`gsc-wochen-check`) als Vorlage. Format: [Agent Skills
Specification](https://agentskills.io/specification).

## Was die Skills nicht tun

- Sie schreiben nichts: keine Sitemaps einreichen, keine Keywords anlegen,
  keine Key Events ändern.
- Sie erfinden keine Zahlen. Kommt vom MCP oder Export nichts zurück, sagen
  sie das.
- Sie ersetzen keine Entscheidung. Bei kleinen Zahlen liefern sie
  Richtungen, keine Prozentrechnungen.

## Lizenz und Autor

MIT. Hagen Krocker, [mcpwerk](https://mcpwerk.com). Fragen und Vorschläge
als Issue in diesem Repo. Das Projektgerüst für ganze SEO-Pipelines liegt
separat unter [`seo-project-template`](https://github.com/Hajgen19/seo-project-template).

---

**English:** Seven German-language SEO skills for Claude, packaged as a
plugin marketplace. Six routine skills pull live data via MCP (Google Search
Console, GA4, Google Ads) or work from CSV exports; the seventh is a full
SERP-first keyword research workflow for Claude Code. Read-only, MIT.
Install in Claude Code with `/plugin marketplace add Hajgen19/claude-seo-skills`
and `/plugin install seo-skills@claude-seo-skills`.

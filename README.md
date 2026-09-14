# Claude SEO Skills

Sechs SEO-Skills für Claude, die ihre Daten **live per MCP** holen statt aus
CSV-Exporten: vier für die Google Search Console, einer für Google Analytics 4,
einer für Google Ads. Jeder Skill ist eine wiederkehrende Aufgabe oder ein
Audit mit festem Ablauf und fester Ausgabe. Alle Skills nutzen nur lesende
Befehle. Deutsch, Du-Form, MIT-Lizenz.

Skills legen den Ablauf fest. Die Daten kommen von einem MCP-Server, der mit
deinem Google-Konto verbunden ist. Entwickelt und getestet gegen die
gehosteten MCP-Server von [mcpwerk](https://mcpwerk.com); die Befehlsnamen in
den Skills sind die von mcpwerk. Mit einem anderen Search-Console-, GA4- oder
Ads-MCP musst du die Befehlsnamen in der jeweiligen `SKILL.md` anpassen.

## Die Skills

| Skill | Was er tut | Rhythmus | Datenquelle |
| --- | --- | --- | --- |
| `gsc-wochen-check` | Zeitraum gegen Vorzeitraum (Standard 7 Tage, auch 28 oder 90), Gewinner- und Verlierer-Seiten, eine Maßnahme | wöchentlich | Search Console |
| `gsc-chancen-keywords` | Suchanfragen mit vielen Impressionen auf Position 8–20 und schwacher Klickrate, mit Title-Empfehlung | monatlich | Search Console |
| `gsc-kannibalisierung` | Suchanfragen, für die zwei oder mehr eigene URLs ranken, mit Schweregrad und Empfehlung | vor neuen Seiten | Search Console |
| `gsc-indexierungs-check` | URL-Liste gegen Indexierungsstatus, nicht indexierte Seiten mit Grund | nach Deploys | Search Console |
| `ga4-zeitraum-vergleich` | Zwei frei wählbare Zeiträume nach Kanal und Landingpage, Gewinner, Verlierer, Potenziale | monatlich | Google Analytics 4 |
| `ads-budget-check` | Keywords und Suchbegriffe mit Kosten ohne Conversions, teure Klicks, Negativ-Vorschläge | wöchentlich | Google Ads |

## Voraussetzungen

1. Ein Claude-Konto mit aktivierter Codeausführung (Skills laufen laut
   Anthropic-Hilfe auf Free, Pro, Max, Team und Enterprise).
2. Ein verbundener MCP-Server für das jeweilige Google-Produkt. Das Plugin
   bringt eine `.mcp.json` mit den drei mcpwerk-Adressen mit
   (`https://mcp.mcpwerk.com/gsc/mcp`, `/ga4/mcp`, `/ads/mcp`). Für die
   Anmeldung brauchst du ein mcpwerk-Konto mit verbundenem Google-Konto;
   die Stufe „Lesen" reicht für alle sechs Skills.

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
benutzerdefinierten Connector mit derselben Adresse.

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
- Sie erfinden keine Zahlen. Kommt vom MCP nichts zurück, sagen sie das.
- Sie ersetzen keine Entscheidung. Bei kleinen Zahlen liefern sie
  Richtungen, keine Prozentrechnungen.

## Lizenz und Autor

MIT. Hagen Krocker, [mcpwerk](https://mcpwerk.com). Fragen und Vorschläge
als Issue in diesem Repo.

---

**English:** Six German-language SEO skills for Claude that pull live data
via MCP (Google Search Console, GA4, Google Ads) instead of CSV exports.
Read-only, MIT. Install in Claude Code with `/plugin marketplace add
Hajgen19/claude-seo-skills` and `/plugin install seo-skills@claude-seo-skills`;
requires an MCP server connected to your Google account (built and tested
against the hosted mcpwerk servers, tool names are theirs).

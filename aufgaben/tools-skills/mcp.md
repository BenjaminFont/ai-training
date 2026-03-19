# Aufgabe: Model Context Protocol (MCP)

MCP-Server erweitern den AI Assistant um externe Datenquellen und Tools — Dokumentation, Tickets, Cloud-Ressourcen, Design-Systeme. Statt Informationen manuell in den Chat zu kopieren, holt Claude sie direkt zur Laufzeit.

---

## Schritt 1: MCP-Server erkunden und Use Case finden (~15 Min)

Schaut euch an, welche MCP-Server es gibt und überlegt, wo einer euren Entwicklungsalltag konkret verbessern würde.

Einstiegspunkte:
- **Figma** — Design-Tokens und Komponenten direkt im Code-Kontext: [Figma MCP](https://help.figma.com/hc/en-us/articles/32132100833559-Guide-to-the-Figma-MCP-server)
- **Jira/Atlassian** — Tickets, Epics und Akzeptanzkriterien als Kontext für Implementierungen: [Atlassian MCP](https://medium.com/@milad.jafary/how-to-connect-atlassian-mcp-server-to-claude-code-5c22d47d5cd5)
- **AWS** — Cloud-Ressourcen, Logs und Infrastruktur-Kontext: [AWS MCP](https://awslabs.github.io/mcp/#available-aws-mcp-servers)
- **Weitere**: [mcp.so](https://mcp.so) und [modelcontextprotocol.io/servers](https://modelcontextprotocol.io/servers)

**Fragestellung:** Gibt es eine Informationsquelle, die ihr heute manuell in den Chat kopiert? Oder eine, die ihr gar nicht nutzt, weil der Aufwand zu groß ist?

Wenn ihr einen passenden Use Case gefunden habt → bindet den Server an (Schritt 2a).
Wenn nicht → nutzt Context7 als Fallback (Schritt 2b).

---

## Schritt 2a: Eigenen MCP-Server anbinden

**Konfigurationsort im JetBrains AI Assistant:**
`Settings → Tools → AI Assistant → Model Context Protocol (MCP) → + Add`

Wechselt auf JSON-Eingabe und tragt die Konfiguration des gewählten Servers ein. Format:

```json
{
  "mcpServers": {
    "name-des-servers": {
      "command": "...",
      "args": ["..."]
    }
  }
}
```

Testet danach: Stellt eine Frage, die den MCP-Server benötigt. Wird er gezogen? Was verändert sich gegenüber einem Prompt ohne Server?

---

## Schritt 2b: Fallback — Context7 anbinden

Context7 liefert Claude aktuelle, versionsspezifische Dokumentation für Libraries und Frameworks — direkt zur Laufzeit, ohne manuelles Copy-Paste aus Changelogs.

**1. API-Key holen:** Registriert euch unter [context7.com](https://context7.com) und erstellt einen kostenlosen API-Key.

**2. Server in JetBrains konfigurieren:**
`Settings → Tools → AI Assistant → Model Context Protocol (MCP) → + Add → JSON`

```json
{
  "mcpServers": {
    "context7": {
      "command": "npx",
      "args": ["-y", "@upstash/context7-mcp", "--api-key", "YOUR_API_KEY"]
    }
  }
}
```

**3. Migration auf Spring Boot 4 bewerten:**

Öffnet den AI Assistant Chat und gebt folgende Aufgabe — mit `@`-Referenz auf eure relevanten Backend-Services:

> "Nutze Context7, um die aktuelle Spring Boot 4 Dokumentation und die Breaking Changes gegenüber Spring Boot 3 abzurufen.
>
> Analysiere dann unsere Backend-Services: @ServiceA @ServiceB @ServiceC
>
> Erstelle ein strukturiertes Migration-Assessment mit:
> 1. **Betroffene Bereiche** — Welche Teile unserer Codebase sind von Breaking Changes betroffen?
> 2. **Aufwandsschätzung** — Grobe Einschätzung pro Bereich (Klein / Mittel / Groß)
> 3. **Risiken** — Wo ist die Migration komplex oder fehleranfällig?
> 4. **Empfohlene Reihenfolge** — In welcher Reihenfolge sollten wir vorgehen?
>
> Stelle die Ergebnisse als Tabelle dar, damit wir sie dem Team präsentieren können."

---

## Reflexion

- Hat der MCP-Server Informationen geliefert, die ihr ohne ihn nicht gehabt hättet?
- Wo hat Claude Lücken im Kontext gehabt, obwohl der Server aktiv war?
- Welcher MCP-Server würde euren Workflow langfristig am meisten verbessern — und warum?
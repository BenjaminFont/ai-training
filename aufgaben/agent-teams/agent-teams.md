# Agent Teams Aufgabe

Agent Teams lassen mehrere Claude Code Instanzen koordiniert zusammenarbeiten. Ein **Lead** koordiniert, verteilt Tasks und fasst Ergebnisse zusammen. Die Teammates arbeiten parallel, jeder in seinem eigenen Context Window.

> ⚠️ Agent Teams sind experimentell und standardmäßig deaktiviert. Benötigt **Claude Code v2.1.32+** (`claude --version`).

## Vorbereitung

**1. Branch anlegen:**
```bash
git checkout -b agent-teams
```

**2. Blueprint-Setup ins Projekt kopieren:**
```bash
cp -r .ai ./
cp -r .claudeTraining/agents/ .claude/agents/
cp -r .claudeTraining/skills/ .claude/skills/
cp -r .claudeTraining/templates/ .claude/templates/
cp -r .claudeTraining/workflows/ .claude/workflows/
cp .claudeTraining/CLAUDE.md .claude/CLAUDE.md
```

**3. Feature in `.claude/settings.json` aktivieren:**
```json
{
  "env": {
    "CLAUDE_CODE_EXPERIMENTAL_AGENT_TEAMS": "1"
  }
}
```

## Das Blueprint-Setup

Das Setup bringt folgende Agents mit: **Lead** (koordiniert), **Architect** (plant), **Developer** (implementiert), **Test Engineer** (Testdesign), **Security Engineer** (Sicherheit), **Reviewer** (Qualität & Commit).

Dazu zwei Skills: `/project-init` (generiert `CLAUDE.md` mit Projektkontext) und `/ensure-plans-dir` (bereitet den Plan-Ordner vor).

## Aufgabe

1. Startet Claude Code und führt `/project-init` aus.
2. Beschreibt dem Lead ein kleines Feature eures Projekts — der Lead führt euch durch Klärung, Workflow-Wahl, Planung und Implementierung.
3. Beobachtet, wie Teammates kommunizieren und Tasks koordinieren.
4. Wenn fertig: bittet den Lead, das Team aufzuräumen (`Clean up the team`).

> Tipp: Mit `Shift+Down` könnt ihr im Terminal zwischen Lead und Teammates wechseln.

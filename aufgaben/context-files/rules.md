# Aufgabe: Project Rules im AI Assistant

Project Rules sind Markdown-Dateien, die Claude projektspezifische Konventionen beibringen — Coding-Standards, Namensregeln, Architekturvorgaben. Der entscheidende Unterschied zu einem Prompt: Ihr schreibt sie einmal, und Claude wendet sie automatisch an.

**Speicherort im Projekt:** `.aiassistant/rules/` (Markdown-Dateien)
**Konfiguration:** `Settings → Tools → AI Assistant → Rules → New Project Rules File`

---

## Die fünf Aktivierungsmodi

| Modus | Wann aktiv | Typischer Einsatz |
|---|---|---|
| **Always** | Bei jeder Chat-Session | Grundlegende Projektkonventionen |
| **By file patterns** | Wenn eine passende Datei offen ist (z.B. `*.kt`) | Sprach- oder Layer-spezifische Regeln |
| **By model decision** | Claude entscheidet anhand einer Beschreibung | Situative Regeln (z.B. "nur bei Sicherheitsfragen") |
| **Manually** | Nur wenn ihr `@rule:dateiname` im Chat schreibt | Regeln, die ihr bewusst einschalten wollt |
| **Off** | Nie | Deaktiviert, aber noch vorhanden |

---

## Schritt 1: Konventionen eures Projekts sammeln

Überlegt: Was müsste Claude über euer Projekt wissen, damit es konsistenten Code schreibt?

Beispiele:
- Namenskonventionen (z.B. Services enden auf `Service`, DTOs auf `Dto`)
- Verbotene Patterns (z.B. "Kein direktes `System.out.println`, immer Logger nutzen")
- Architekturvorgaben (z.B. "Services dürfen nicht direkt andere Services injizieren, nur über Interfaces")
- Test-Konventionen (z.B. "Unit-Tests mit JUnit 5, Mocking nur mit Mockito")

Schreibt mindestens fünf solcher Regeln auf.

---

## Schritt 2: Rules-Dateien anlegen

Gruppiert eure Regeln thematisch und legt je eine Datei an:

```
.aiassistant/rules/
├── coding-conventions.md
├── architecture.md
└── testing.md
```

**Beispiel für `coding-conventions.md`:**

```markdown
# Coding Conventions

- Services enden immer auf `Service`, Repositories auf `Repository`
- Kein `System.out.println` — immer SLF4J Logger verwenden
- Methoden haben maximal 20 Zeilen, danach auslagern
```

Wählt für jede Datei den passenden Aktivierungsmodus:
- Konventionen, die immer gelten → **Always**
- Kotlin-spezifische Regeln → **By file patterns** (`*.kt`)
- Testing-Regeln → **By model decision** mit Beschreibung "Aktiv wenn Tests geschrieben oder reviewt werden"

---

## Schritt 3: Testen

**Test A — Regel greift automatisch:**
Öffnet eine relevante Datei, stellt Claude eine Implementierungsaufgabe ohne Erwähnung eurer Konventionen. Hält Claude sich daran?

**Test B — Regel greift nicht, wo sie nicht soll:**
Stellt eine Frage, die nichts mit dem Thema der Regel zu tun hat. Taucht die Regel trotzdem auf? (Bei `Always` ja — überlegt ob das sinnvoll ist.)

**Test C — Manuelle Aktivierung:**
Setzt eine Regel auf `Manually` und ruft sie explizit im Chat auf:
> "@rule:architecture Ist dieser Service-Entwurf regelkonform? @ServiceX"

---

## Reflexion

- Welche Regeln haben tatsächlich das Verhalten von Claude verändert?
- Was gehört in eine Rule — und was besser in den Prompt?
- Würdet ihr diese Dateien in euer Git-Repository committen, damit das ganze Team davon profitiert?

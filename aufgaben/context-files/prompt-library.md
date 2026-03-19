# Aufgabe: Prompt Library — Eigene Prompts für euer Team

Im JetBrains AI Assistant könnt ihr wiederkehrende Prompts als Custom Prompts in der Prompt Library speichern. Statt denselben Prompt immer neu zu tippen, ruft ihr ihn mit zwei Klicks auf — direkt auf dem markierten Code.

**Konfigurationsort:** `Settings → Tools → AI Assistant → Prompt Library → +`

---

## Was ihr bauen sollt

Erstellt mindestens drei Custom Prompts für Aufgaben, die ihr in eurem Projekt regelmäßig macht.

Die Variable `$SELECTION` fügt automatisch den markierten Code ein — ihr müsst ihn nicht kopieren.

---

## Schritt 1: Drei Kandidaten identifizieren

Überlegt: Welche Prompts tippt ihr immer wieder?

Beispiele zur Inspiration — aber nutzt echte Fälle aus eurem Projekt:

**Code Review:**
> "Reviewe diesen Code aus der Perspektive eines Senior-Entwicklers. Fokus: Fehlerbehandlung, Lesbarkeit, mögliche Edge Cases. Gib konkrete Verbesserungsvorschläge:\n\n$SELECTION"

**Test-Skeleton:**
> "Schreibe ein Unit-Test-Skeleton für diesen Code. Nutze unser Test-Framework. Zeige nur die Testmethoden mit sprechenden Namen und TODO-Kommentaren — noch keinen Implementierungscode:\n\n$SELECTION"

**Erklärung für PR-Beschreibung:**
> "Erkläre diese Änderung in 3-5 Sätzen so, als würdest du sie in einer Pull-Request-Beschreibung für dein Team dokumentieren:\n\n$SELECTION"

---

## Schritt 2: Prompts anlegen

Für jeden Prompt:
1. `Settings → Tools → AI Assistant → Prompt Library → +`
2. Namen vergeben (erscheint im AI Actions-Menü)
3. Prompt-Text schreiben mit `$SELECTION` an der richtigen Stelle
4. Entscheiden: "Wait for additional user input" aktivieren? (Sinnvoll wenn ihr noch eine Frage ergänzen wollt)

---

## Schritt 3: Testen

Markiert Code in eurem Projekt, öffnet das AI Actions-Menü (Rechtsklick → AI Actions oder `Alt+Enter`) und ruft jeden eurer Prompts auf.

Bewertet:
- Ist der Output direkt nutzbar oder braucht es Nacharbeit?
- Ist der Prompt zu allgemein oder zu eng?
- Fehlt Kontext, den ihr noch ergänzen müsstet?

Überarbeitet die Prompts bis sie für euren Alltag passen.

---

## Ergebnis

Ihr habt eine kleine team-spezifische Prompt Library für wiederkehrende Aufgaben. Überlegt am Schluss: Welche Prompts wären sinnvoll für alle im Team — und wie würdet ihr diese standardisieren?
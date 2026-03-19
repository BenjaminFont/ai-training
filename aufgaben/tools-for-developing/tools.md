# Aufgabe: Modellwahl — Haiku, Sonnet oder Opus?

Nicht jede Aufgabe braucht das stärkste Modell. Der JetBrains AI Assistant läuft standardmäßig mit Sonnet, aber ihr könnt jederzeit ein anderes Modell wählen. Die Wahl hat konkrete Auswirkungen auf Geschwindigkeit, Kosten und Qualität.

Eure Aufgabe:
Führt dieselbe Aufgabe dreimal aus — einmal mit Haiku, einmal mit Sonnet, einmal mit Opus — und bewertet die Ergebnisse.

## Welche Modelle gibt es?

| Modell     | Model-ID                     | Stärken                                              |
|------------|------------------------------|------------------------------------------------------|
| Haiku 4.5  | `claude-haiku-4-5-20251001`  | Schnell, günstig, gut für einfache, repetitive Tasks |
| Sonnet 4.6 | `claude-sonnet-4-6`          | Ausgewogen — Standardwahl für die meisten Aufgaben   |
| Opus 4.6   | `claude-opus-4-6`            | Stärkste Reasoning-Fähigkeiten, für komplexe Analyse |

## Aufgabe

Sucht in eurem Projekt eine mittelharte Aufgabe — zum Beispiel:
- "Erkläre mir diesen Service und welche Abhängigkeiten er hat"
- "Schreibe Unit-Tests für diese Klasse"
- "Refactore diese Methode nach Clean-Code-Prinzipien"

Führt diese Aufgabe dreimal aus, jeweils mit einem anderen Modell.

**Modell wechseln im JetBrains AI Assistant:**
1. AI Assistant Chat öffnen
2. Im Chat-Fenster oben das Modell-Dropdown anklicken
3. Claude-Modell auswählen (Haiku / Sonnet / Opus)
4. Dieselbe Frage stellen und Antwort notieren

Alternativ: `Settings → Tools → AI Assistant → AI Model`

## Was ihr beobachten sollt

Notiert für jedes Modell:
1. **Geschwindigkeit** — Wie schnell kommt die Antwort?
2. **Vollständigkeit** — Wie viel Kontext zieht das Modell heran?
3. **Qualität** — Ist das Ergebnis direkt nutzbar oder braucht es Nacharbeit?
4. **Überraschungen** — Wo war Haiku besser als erwartet? Wo hat Opus enttäuscht?

## Empfehlungen für euren Alltag

Überlegt anhand eurer Beobachtungen:
- Für welche Aufgaben in eurem Projekt reicht Haiku?
- Wo ist Sonnet die richtige Wahl?
- Wann lohnt sich Opus?

Schreibt eine kurze Entscheidungsregel (3–5 Sätze) die ihr in eure CLAUDE.md aufnehmen könntet, damit Claude künftig selbst das passende Modell empfiehlt oder ihr wisst, wann ihr manuell wechseln solltet.
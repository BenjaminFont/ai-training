# Aufgabe: Prompt Engineering

Drei Übungen, die aufeinander aufbauen. Ihr arbeitet direkt im JetBrains AI Assistant mit eurem eigenen Projekt und Repo — so seht ihr sofort, ob ein Prompt funktioniert oder nicht.

**Gesamtzeit: ~40 Minuten**

---

## Übung 1: Grundprinzipien — Rolle, Kontext, Aufgabe, Format 

Ein guter Prompt besteht aus vier Bausteinen:

| Baustein    | Frage                          | Beispiel                                                    |
|-------------|--------------------------------|-------------------------------------------------------------|
| **Rolle**   | Wer soll Claude sein?          | "Du bist ein Senior Backend-Entwickler in unserem Team."    |
| **Kontext** | Was ist der Hintergrund?       | "Wir nutzen Spring Boot, Java 21, Postgres."                |
| **Aufgabe** | Was soll konkret passieren?    | "Reviewe diese Methode auf Fehlerbehandlung."               |
| **Format**  | Wie soll die Antwort aussehen? | "Gib eine nummerierte Liste mit max. 5 Punkten aus."        |

**Schritt 1:** Öffnet den AI Assistant Chat in JetBrains. Stellt eine Frage zu einer Klasse oder Methode aus eurem Projekt — aber mit einem schlechten Prompt:

> "Was ist falsch an diesem Code?"

Notiert die Antwort (Qualität, Relevanz, Tiefe).

**Schritt 2:** Startet eine neue Konversation. Formuliert denselben Prompt mit allen vier Bausteinen und fügt den Code als Kontext ein (per `@`-Referenz oder Copy-Paste).

Vergleicht die Ergebnisse. Was hat sich verändert?

---

## Übung 2: System Prompt vs. User Prompt 

Im JetBrains AI Assistant gibt es zwei Orte für Anweisungen:

- **Custom Instructions (System Prompt)** — dauerhafte Anweisungen, die für jede Konversation gelten. Konfigurierbar unter `Settings → Tools → AI Assistant → Rules`.
- **User Prompt** — einmalige Anweisung direkt im Chat für eine konkrete Aufgabe.

**Die Faustregel:** Wenn ihr euch denselben Satz in jedem Prompt wiederholt, gehört er in die Custom Instructions.

**Schritt 1:** Überlegt, welche Infos Claude grundsätzlich über euer Projekt braucht:
- Welche Sprache / welches Framework?
- Welche Konventionen gelten immer (z.B. "Antworte immer auf Deutsch", "Nutze keine Lombok-Annotations")?
- Was ist der Zweck des Projekts?

**Schritt 2:** Tragt mindestens zwei dieser Punkte in die Custom Instructions ein (`Settings → Tools → AI Assistant → Rules`).

**Schritt 3:** Stellt Claude danach eine Frage, bei der diese Infos relevant sind — ohne sie im Prompt nochmal zu erwähnen. Greift Claude auf die Custom Instructions zurück?

---

## Übung 3: Iteratives Prompting 

Selten ist der erste Prompt perfekt. Iteratives Prompting heißt: bewusst vage starten, dann schrittweise präzisieren — und beobachten, wie sich die Antwort verändert.

**Schritt 1 — Vage starten:**
Wählt eine konkrete Aufgabe aus eurem Projekt (z.B. eine Klasse refactoren, Tests schreiben, Dokumentation ergänzen). Referenziert die Datei im AI Assistant Chat mit `@Dateiname` und stellt einen minimalen Prompt:

> "Verbessere das hier."

**Schritt 2 — Erste Präzisierung:**
Ergänzt den Prompt um Kontext und Ziel:

> "Refactore diese Klasse so, dass sie dem Single-Responsibility-Prinzip folgt. Erkläre kurz, was du geändert hast und warum."

**Schritt 3 — Zweite Präzisierung:**
Schränkt Format oder Scope weiter ein, z.B.:

> "Mach nur die Methode `processOrder` — und zeig mir die geänderte Version direkt als Code ohne weitere Erklärung."

Notiert nach jedem Schritt: Wie hat sich die Antwort verändert? Wann war sie nutzbar?

---

## Ergebnis

Am Ende habt ihr:
- Einen verbesserten Prompt aus Übung 1, den ihr wiederverwenden könnt
- Custom Instructions im AI Assistant mit dauerhaft nützlichem Kontext für euer Projekt
- Ein Gefühl dafür, wann weitere Präzisierung Mehrwert bringt — und wann nicht

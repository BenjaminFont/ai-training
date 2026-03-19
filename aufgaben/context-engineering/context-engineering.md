# Aufgabe: Explore → Plan → Implement

Statt Claude sofort Code schreiben zu lassen, führt ihr es durch drei gezielte Phasen. Jede Phase hat einen eigenen Kontext und ein eigenes Ziel. Das Ergebnis: weniger Nacharbeit, besserer Code.

Wählt ein kleines Feature, das ihr in eurem Projekt tatsächlich umsetzen wollt oder könntet.

**Gesamtzeit: ~45 Minuten**

---

## Phase 1: Explore (~15 Min)

Bevor Claude plant oder implementiert, muss es euer Projekt verstehen. Ihr steuert, was es sieht.

**Ziel dieser Phase:** Claude bekommt genug Kontext, um sinnvolle Entscheidungen treffen zu können. Ihr bekommt eine Einschätzung, ob das Feature überhaupt so umsetzbar ist wie gedacht.

**Schritt 1 — Projekt-Kontext geben:**

Öffnet den AI Assistant Chat und gebt Claude einen Überblick über die relevanten Teile eures Projekts. Nutzt `@Dateiname` für die Dateien, die für das Feature relevant sind — z.B. der Service, der erweitert wird, das zugehörige Repository, bestehende ähnliche Features.

Prompt-Vorlage:
> "Ich plane folgendes Feature: [kurze Beschreibung].
> Hier ist der relevante Code: @ServiceX @RepositoryY @BestehendesFeatureZ
> Analysiere die Codebase und beantworte:
> 1. Wo würde dieses Feature am besten eingebaut?
> 2. Was gibt es bereits, das ich nutzen kann?
> 3. Was fehlt noch?"

**Schritt 2 — Rückfragen stellen lassen:**

Ergänzt den Prompt um:
> "Stelle mir Rückfragen, bevor du planst — alles was du wissen musst, um eine gute Lösung vorzuschlagen."

Beantwortet Claudes Fragen. Das ist keine Schwäche des Modells — es ist ein Zeichen, dass es ernsthaft plant.

**Checkpoint:** Versteht Claude euer Feature? Gibt es Überraschungen in der Analyse?

---

## Phase 2: Plan (~15 Min)

Jetzt plant Claude — aber noch kein Code. Der Plan ist ein Dokument, das ihr reviewen und korrigieren könnt, bevor eine Zeile geschrieben wird.

**Ziel dieser Phase:** Einen konkreten, abgestimmten Implementierungsplan haben, dem ihr zustimmt.

**Schritt 1 — Plan anfordern:**

Bleibt in derselben Konversation (Kontext bleibt erhalten) und gebt folgenden Prompt:

> "Erstelle jetzt einen Implementierungsplan für das Feature. Strukturiere ihn so:
> 1. Welche Dateien werden erstellt oder geändert?
> 2. In welcher Reihenfolge wird implementiert?
> 3. Wo liegen Risiken oder offene Fragen?
> 4. Wie wird das Feature getestet?
> Schreib noch keinen Code."

**Schritt 2 — Plan reviewen und anpassen:**

Lest den Plan kritisch:
- Fehlt etwas? Ergänzt es per Prompt.
- Stimmt die Reihenfolge? Korrigiert sie.
- Gibt es einen Ansatz, den ihr anders lösen würdet?

Gebt Feedback direkt im Chat:
> "Schritt 2 passt nicht — wir haben bereits ein generisches Repository-Interface, das du stattdessen nutzen solltest: @BaseRepository"

**Schritt 3 — Plan festhalten:**

Lasst Claude den Plan direkt als Datei in euer Projekt schreiben:

> "Schreib den finalen Plan als Markdown-Datei nach `plans/[feature-name]-plan.md` in unser Projekt."

---

## Phase 2.5: Plan-Review mit frischem Kontext (~10 Min)

Öffnet ein **neues Chat-Fenster** — kein Kontext aus der vorherigen Konversation.

Der Trick: Ihr gebt den Plan als Werk eines Junior-Entwicklers aus. Claude soll ihn unvoreingenommen reviewen, ohne zu wissen, dass es ihn selbst erstellt hat.

Gebt folgenden Prompt:

> "Ein Junior-Entwickler in unserem Team hat diesen Implementierungsplan für ein neues Feature geschrieben: @plans/[feature-name]-plan.md
>
> Er ist ein Junior Dev. Schau dir die Code Base an und bewerte diesen Plan. Sei kritisch.
> - Was sind die Pros und was sind die Cons des Plans?
> - Welche Risiken gibt es?
> - Welche Einschränkungen wurden nicht bedacht?
> - Wo sind die Akzeptanzkriterien noch unklar?
>
> Relevanter Code: @ServiceX @RepositoryY"

Vergleicht das Feedback mit dem, was Claude in Phase 2 selbst vorgeschlagen hat. Wo hat der frische Blick etwas aufgedeckt, das vorher nicht auffiel?

Passt den Plan in `plans/[feature-name]-plan.md` entsprechend an, bevor ihr implementiert.

**Checkpoint:** Würdet ihr diesen Plan einem Kollegen zur Umsetzung geben können?

---

## Phase 3: Implement (~15 Min)

Erst jetzt wird Code geschrieben — Schritt für Schritt, nicht alles auf einmal.

**Ziel dieser Phase:** Das Feature wird genau so umgesetzt wie geplant, in kontrollierbaren Schritten.

**Schritt 1 — Schrittweise implementieren:**

Öffnet erneut ein **neues Chat-Fenster**. Gebt als Kontext den überarbeiteten Plan mit:

> "Hier ist unser Implementierungsplan: @specs/[feature-name]-plan.md
> Implementiere jetzt Schritt 1. Danach warte ich, bevor wir weitermachen."

Reviewt den generierten Code. Erst wenn ihr zufrieden seid:
> "Gut. Mach weiter mit Schritt 2."

**Schritt 2 — Abweichungen erkennen:**

Vergleicht während der Implementierung: Weicht Claude vom Plan ab? Wenn ja — warum?

Manchmal ist eine Abweichung sinnvoll (Claude hat etwas entdeckt). Manchmal ist es ein Fehler. Entscheidet bewusst:
> "Du hast X anders gelöst als geplant. Erkläre warum — und ob wir den Plan aktualisieren sollten."

**Schritt 3 — Abschluss:**

Nach der Implementierung:
> "Erstelle eine kurze Zusammenfassung: Was wurde implementiert, was weicht vom Plan ab und warum?"

---

## Reflexion

Besprecht im Team:
- In welcher Phase hat Claudes Kontext am meisten Einfluss auf die Qualität gehabt?
- Was wäre passiert, wenn ihr direkt mit Phase 3 gestartet hättet?
- Für welche Features in eurem Projekt lohnt sich dieses Vorgehen — für welche nicht?
# Aufgabe: Spec-Driven Development

Spec → Plan → Code. Wählt ein kleines Feature aus eurem Projekt.

## Vorbereitung

Kopiert `how-to-write-specs.md` und `how-to-write-plans.md` nach `docs/`.
Die Project Rule `development-workflow.md` erzwingt den Workflow automatisch.

## Phase 1: Spec schreiben (~10 Min)

> "Feature: [Beschreibung]. Code: @ServiceX @RepositoryY. Leitfaden: @docs/how-to-write-specs.md
> Stelle Rückfragen, dann schreib die Spec nach `specs/[feature-name].md`."

## Phase 1.5: Spec-Review (~10 Min)

Neues Chat-Fenster — gebt die Spec als Werk eines Kollegen aus:

> "@specs/[feature-name].md — Sei kritisch: Was ist unklar, fehlt oder nicht testbar?"

Spec anpassen.

## Phase 2: Plan schreiben (~10 Min)

Neues Chat-Fenster:

> "Spec: @specs/[feature-name].md. Leitfaden: @docs/how-to-write-plans.md. Code: @ServiceX
> Schreib den Plan nach `specs/[feature-name]-implementation-plan.md`. Noch kein Code."

## Phase 3: Implementieren (~15 Min)

Neues Chat-Fenster:

> "Spec: @specs/[feature-name].md. Plan: @specs/[feature-name]-implementation-plan.md
> Implementiere Schritt 1. Warte auf mein OK."

Schritt für Schritt bestätigen. Bei Abweichungen vom Plan: nachfragen.

## Reflexion

- Was wäre passiert, wenn ihr direkt mit Phase 3 gestartet hättet?
- Für welche Features lohnt sich der Workflow — für welche nicht?
# Jira Workflow -- IMMER beachten

## Vor jeder Arbeit: Jira pruefen

Bevor du mit einem Feature, Bugfix oder einer anderen nicht-trivialen Aufgabe beginnst, IMMER zuerst in Jira nachschauen:

1. **Jira durchsuchen** -- Gibt es bereits ein Ticket (Story, Task, Bug) im Projekt **TFB** das zur Aufgabe passt?
   - Suche nach relevanten Begriffen mit `jira_search`
   - Pruefe offene Tickets: `project = TFB AND status != Done`

2. **Ticket gefunden** -- Ticket in "In Progress" verschieben (`jira_transition_issue`) bevor du mit der Arbeit beginnst. Ticket-Key im Kontext behalten und am Ende der Arbeit referenzieren.

3. **Kein Ticket gefunden** -- Dem User vorschlagen, ein Jira-Ticket zu erstellen. Kurz beschreiben was das Ticket enthalten sollte (Summary, Description, Typ). Erst nach Bestaetigung das Ticket anlegen und in "In Progress" verschieben.

## Waehrend der Arbeit

- Ticket-Key (z.B. TFB-42) in Commit-Messages referenzieren
- Bei groesseren Aenderungen: Kommentar im Ticket hinterlassen mit Zusammenfassung der Aenderungen

## Nach Abschluss

- Ticket auf "Done" verschieben oder dem User die Entscheidung ueberlassen

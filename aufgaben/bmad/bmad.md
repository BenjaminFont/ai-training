# BMAD Aufgabe

BMAD (Breakthrough Method for Agile AI-Driven Development) ist ein Framework das AI-assistierte Entwicklung mit minimalem menschlichem Eingriff optimiert. Das Ziel: Intent rein, Code-Änderungen raus, mit so wenigen Rückfragen wie möglich.

Mehr dazu: https://docs.bmad-method.org/explanation/quick-dev/

## Vorbereitung

1. Legt in eurem Projekt einen neuen Branch an:
   ```
   git checkout -b bmad
   ```

2. Installiert BMAD (Bleeding Edge) im Projektverzeichnis:
   ```
   npx github:bmad-code-org/BMAD-METHOD install
   ```
   Hinweis: Diese Version zieht direkt vom `main`-Branch und kann instabil sein.
   Folgt dem interaktiven Setup-Dialog und wählt die für euer Projekt passenden Optionen.

## Aufgabe

1. Lest die Quick-Dev-Dokumentation: https://docs.bmad-method.org/explanation/quick-dev/
   Verschafft euch einen Überblick, wie BMAD den Entwicklungsprozess strukturiert
   (Intent Compression, Smart Routing, Extended Autonomy).

2. Probiert den Quick-Dev-Workflow an einem kleinen Feature eures Projekts aus:
   Gebt BMAD einen groben Intent und beobachtet, wie das Framework die Anfrage
   vor der Implementierung aufbereitet und strukturiert.

3. Vergleicht: Was macht BMAD anders als euer bisheriger Workflow mit Claude Code?
   Notiert zwei bis drei konkrete Unterschiede.

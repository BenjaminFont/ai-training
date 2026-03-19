# SERH WICHTIG Development Workflow

## Spec -> Plan -> Implement 

Bei nicht-trivialen Aenderungen gilt IMMER diese Reihenfolge:

1. **Spec schreiben** (`/spec`) -- WAS und WARUM. Speichern unter `specs/<feature>.md`
2. **Plan schreiben** (`/plan`) -- WIE und WANN. Speichern unter `specs/<feature>-implementation-plan.md`
3. **Implementieren** -- auf Basis des Plans, schrittweise

Siehe `docs/how-to-write-specs.md` und `docs/how-to-write-plans.md` fuer Details.

## WANN den vollen Workflow durchlaufen 

| Aenderung | Workflow |
|-----------|----------|
| Neues Feature, neue API, neue Entitaet | Spec -> Plan -> Implement |
| Migration (DB, Framework, Library) | Spec -> Plan -> Implement |
| Groesserer Bugfix (Ursache unklar, mehrere Dateien) | Plan -> Implement (Spec optional) |
| Refactoring ueber mehrere Dateien | Plan -> Implement |
| Kleiner Bugfix (Ursache bekannt, 1-2 Dateien) | Direkt implementieren |
| Config-Aenderung, Typo, Dependency-Update | Direkt implementieren |
| Einzeiler, Rename, Formatting | Direkt implementieren |

## Faustregel

Wenn die Aenderung mehr als 3 Dateien betrifft oder die Herangehensweise nicht sofort klar ist, mindestens einen Plan schreiben. Wenn das Feature neu ist und andere es verstehen muessen, eine Spec schreiben.

## Waehrend der Implementierung

- Plan als Leitfaden nutzen, nicht als starre Checkliste
- Plan aktualisieren wenn sich neue Erkenntnisse ergeben
- Nach Abschluss: Plan kann geloescht oder als Referenz behalten werden

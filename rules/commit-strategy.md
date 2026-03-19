# Commit-Strategie -- Kleine, haeufige Commits

## Grundregel

Commits sollen **klein, atomar und haeufig** sein. Jeder Commit beschreibt EINE abgeschlossene logische Einheit.

## Wann committen

- Nach jeder abgeschlossenen Subtask / Teilaufgabe
- Nach jedem funktionierenden Zwischenschritt (z.B. Entity angelegt, Service implementiert, Test gruen)
- Bevor du zum naechsten logischen Schritt uebergehst
- Lieber ein Commit zu viel als eins zu wenig

## Commit-Groesse

- Ein Commit = eine logische Aenderung (z.B. "Add Player entity", "Add PlayerService with create method", "Add validation for skill ratings")
- NICHT mehrere unabhaengige Aenderungen in einen Commit packen
- NICHT warten bis alles fertig ist und dann einen grossen Commit machen

## Commit-Messages

- Kurz und praezise, im Imperativ ("Add ...", "Fix ...", "Update ...")
- Jira-Ticket referenzieren wenn vorhanden (z.B. "TFB-42: Add player skill validation")
- Format: `TFB-<nr>: <beschreibung>` oder ohne Ticket-Nr wenn keins existiert

## Beispiel-Ablauf fuer ein Feature

```
TFB-42: Add PlayerSkill entity and repository
TFB-42: Add PlayerSkillService with CRUD operations
TFB-42: Add unit tests for PlayerSkillService
TFB-42: Add PlayerSkillController endpoints
TFB-42: Add controller integration tests
TFB-42: Add frontend skill editor component
TFB-42: Wire up skill editor to API
```

Jeder dieser Commits ist eigenstaendig, nachvollziehbar und klein genug um ihn leicht zu reviewen.

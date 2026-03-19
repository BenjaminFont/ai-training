# Testing Rules

## Grundregeln

- Neuer Code braucht Tests. Keine Ausnahmen.
- Jeder Test ist isoliert -- keine Abhaengigkeiten zwischen Tests
- Keine echten API-Calls, keine echten DB-Calls in Unit Tests

## Naming

Testname beschreibt das erwartete Verhalten, nicht die Methode:
- `shouldReturnUser_whenIdExists` statt `testGetUser`
- `renders error message when login fails` statt `test login component`

## Struktur

- Arrange-Act-Assert (Backend) / Given-When-Then
- Setup und Teardown ueber Framework-Mechanismen, nicht manuell
- Ein logischer Assert pro Test

## Testdaten

- Factories oder Builder verwenden, nicht inline hardcoden
- Keine magischen Werte ohne Erklaerung
- Shared Fixtures nur wenn wirklich mehrere Tests identische Daten brauchen

## Was NICHT tun

- Keine Business-Logik in Tests (kein if/else, keine Loops)
- Keine Test-Reihenfolge-Abhaengigkeiten
- Kein `Thread.sleep()` oder feste Timeouts -- async Utilities verwenden
- Keine Tests die immer gruen sind (assert muss fehlschlagen koennen)

---
paths:
  - "src/test/**/*.java"
  - "src/test/**/*.kt"
---

# Backend Testing Rules

## Stack
JUnit 5, Mockito, Spring Boot Test, H2 (Test-DB), Flyway

## Test-Typen nach Schicht

| Schicht | Test-Typ | Annotation |
|---------|----------|------------|
| Controller | @WebMvcTest | MockMvc, Security-Kontext mocken |
| Service | Unit Test | @ExtendWith(MockitoExtension.class), keine Spring-Context |
| Repository | @DataJpaTest | H2 In-Memory, Flyway-Migrationen laufen mit |
| Integration | @SpringBootTest | Voller Context, sparsam einsetzen |

## Konventionen

- Testklasse: `{Klassenname}Test.java` im gleichen Package unter `src/test/java`
- Methoden-Naming: `shouldDoSomething_whenCondition`
- Arrange-Act-Assert Struktur, durch Leerzeilen getrennt
- Ein Assert pro Test (logisch, nicht strikt)
- Keine Testdaten hardcoden -- Builder oder Factory-Methoden verwenden

## Mocking

- Services: `@Mock` fuer Abhaeingkeiten, `@InjectMocks` fuer SUT
- Controller: `@MockBean` fuer Service-Layer
- Externe Services (S3, etc.): Immer mocken, nie echte Calls
- Repository-Tests: NICHT mocken, H2 nutzen

## Security in Tests

- `@WithMockUser(roles = "USER")` fuer authentifizierte Requests
- Security-relevante Endpoints: Sowohl autorisiert als auch unautorisiert testen
- JWT-Token in Integrationstests ueber TestHelper erzeugen

## Was NICHT tun

- Keinen Spring-Context starten wenn Unit Test reicht
- Keine `@SpringBootTest` fuer einzelne Service-Methoden
- Kein `Thread.sleep()` -- `Awaitility` verwenden wenn async
- Keine Test-Reihenfolge-Abhaengigkeiten (`@Order` vermeiden)

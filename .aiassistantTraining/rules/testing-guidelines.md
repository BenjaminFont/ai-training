---
paths:
  - "team-finder-backend/src/test/**/*.java"
  - "team-finder-frontend/**/*.spec.ts"
  - "team-finder-frontend/**/*.test.ts"
  - "team-finder-frontend/cypress/**"
---

# Testing Guidelines

## Overview

Team Finder employs comprehensive testing strategies across both frontend and backend:
- **Backend**: JUnit 5, Mockito, Spring Boot Test
- **Frontend**: Vitest, Vue Test Utils, Cypress
- **TDD Methodology**: Red-Green-Refactor with Human-in-the-Loop

## Backend Testing

### Service Layer Testing

#### Test Class Structure

```java
@ExtendWith(MockitoExtension.class)
@DisplayNameGeneration(DisplayNameGenerator.ReplaceUnderscores.class)
class PlayerServiceTest {

    @Mock
    private PlayerRepository playerRepository;

    @Mock
    private GroupService groupService;

    @Mock
    private SecurityUtils securityUtils;

    @InjectMocks
    private PlayerService playerService;

    private Player testPlayer;
    private PlayerDto testPlayerDto;
    private AppUser testUser;

    @BeforeEach
    void setUp() {
        testUser = AppUser.builder()
            .id(1L)
            .username("testuser")
            .role(UserRole.USER)
            .build();

        testPlayer = Player.builder()
            .id(1L)
            .name("Test Player")
            .rating(75)
            .user(testUser)
            .build();

        testPlayerDto = PlayerDto.builder()
            .id(1L)
            .name("Test Player")
            .rating(75)
            .userId(1L)
            .build();
    }
}
```

#### Testing Patterns

**Happy Path Testing**
```java
@Test
void should_return_player_dto_when_valid_id_provided() {
    // Given
    when(playerRepository.findById(1L)).thenReturn(Optional.of(testPlayer));

    // When
    PlayerDto result = playerService.getPlayerById(1L);

    // Then
    assertThat(result).isNotNull();
    assertThat(result.getId()).isEqualTo(1L);
    assertThat(result.getName()).isEqualTo("Test Player");

    verify(playerRepository, times(1)).findById(1L);
}
```

**Exception Testing**
```java
@Test
void should_throw_entity_not_found_exception_when_player_does_not_exist() {
    // Given
    Long nonExistentId = 999L;
    when(playerRepository.findById(nonExistentId)).thenReturn(Optional.empty());

    // When & Then
    assertThatThrownBy(() -> playerService.getPlayerById(nonExistentId))
        .isInstanceOf(EntityNotFoundException.class)
        .hasMessageContaining("Player")
        .hasMessageContaining(nonExistentId.toString());

    verify(playerRepository, times(1)).findById(nonExistentId);
}
```

**Security Testing**
```java
@Test
void should_throw_access_denied_when_user_lacks_permission() {
    // Given
    when(securityUtils.getCurrentUser()).thenReturn(Optional.of(testUser));
    when(playerRepository.findById(1L)).thenReturn(Optional.of(testPlayer));

    AppUser differentUser = AppUser.builder()
        .id(2L)
        .username("otheruser")
        .role(UserRole.USER)
        .build();
    testPlayer.setUser(differentUser);

    // When & Then
    assertThatThrownBy(() -> playerService.updatePlayer(1L, new UpdatePlayerRequest()))
        .isInstanceOf(AccessDeniedException.class)
        .hasMessageContaining("permission");

    verify(playerRepository, never()).save(any());
}
```

### Controller Layer Testing

#### MockMvc Setup

```java
@WebMvcTest(PlayerController.class)
@DisplayNameGeneration(DisplayNameGenerator.ReplaceUnderscores.class)
class PlayerControllerTest {

    @Autowired
    private MockMvc mockMvc;

    @MockBean
    private PlayerService playerService;

    @MockBean
    private JwtTokenProvider tokenProvider;

    private ObjectMapper objectMapper = new ObjectMapper();

    @Test
    @WithMockUser(roles = "USER")
    void should_return_all_players_when_get_request() throws Exception {
        // Given
        List<PlayerDto> players = Arrays.asList(
            createPlayerDto(1L, "Player 1"),
            createPlayerDto(2L, "Player 2")
        );
        when(playerService.getAllPlayers()).thenReturn(players);

        // When & Then
        mockMvc.perform(get("/api/players")
                .contentType(MediaType.APPLICATION_JSON))
            .andExpect(status().isOk())
            .andExpect(jsonPath("$", hasSize(2)))
            .andExpect(jsonPath("$[0].name").value("Player 1"))
            .andExpect(jsonPath("$[1].name").value("Player 2"));

        verify(playerService, times(1)).getAllPlayers();
    }
}
```

#### Request/Response Testing

```java
@Test
@WithMockUser(roles = "USER")
void should_create_player_when_valid_request() throws Exception {
    // Given
    CreatePlayerRequest request = CreatePlayerRequest.builder()
        .name("New Player")
        .rating(80)
        .skills(Arrays.asList(
            SkillDto.builder().skill("Speed").rating(85).build()
        ))
        .build();

    PlayerDto createdPlayer = PlayerDto.builder()
        .id(1L)
        .name("New Player")
        .rating(80)
        .build();

    when(playerService.createPlayer(any(CreatePlayerRequest.class)))
        .thenReturn(createdPlayer);

    // When & Then
    mockMvc.perform(post("/api/players")
            .contentType(MediaType.APPLICATION_JSON)
            .content(objectMapper.writeValueAsString(request)))
        .andExpect(status().isCreated())
        .andExpect(header().exists("Location"))
        .andExpect(jsonPath("$.id").value(1))
        .andExpect(jsonPath("$.name").value("New Player"));

    verify(playerService, times(1)).createPlayer(any(CreatePlayerRequest.class));
}
```

#### Validation Testing

```java
@Test
@WithMockUser(roles = "USER")
void should_return_bad_request_when_validation_fails() throws Exception {
    // Given
    CreatePlayerRequest invalidRequest = CreatePlayerRequest.builder()
        .name("") // Invalid: empty name
        .rating(150) // Invalid: exceeds max
        .build();

    // When & Then
    mockMvc.perform(post("/api/players")
            .contentType(MediaType.APPLICATION_JSON)
            .content(objectMapper.writeValueAsString(invalidRequest)))
        .andExpect(status().isBadRequest())
        .andExpect(jsonPath("$.validationErrors").exists())
        .andExpect(jsonPath("$.validationErrors.name").value("Name is required"))
        .andExpect(jsonPath("$.validationErrors.rating").value("Rating cannot exceed 100"));

    verify(playerService, never()).createPlayer(any());
}
```

### Integration Testing

```java
@SpringBootTest
@AutoConfigureMockMvc
@Transactional
@TestPropertySource(locations = "classpath:application-test.properties")
class PlayerIntegrationTest {

    @Autowired
    private MockMvc mockMvc;

    @Autowired
    private PlayerRepository playerRepository;

    @Test
    @WithMockUser(username = "testuser", roles = "USER")
    void should_create_and_retrieve_player() throws Exception {
        // Create player
        CreatePlayerRequest request = createValidPlayerRequest();

        MvcResult createResult = mockMvc.perform(post("/api/players")
                .contentType(MediaType.APPLICATION_JSON)
                .content(objectMapper.writeValueAsString(request)))
            .andExpect(status().isCreated())
            .andReturn();

        PlayerDto createdPlayer = objectMapper.readValue(
            createResult.getResponse().getContentAsString(),
            PlayerDto.class
        );

        // Verify in database
        Optional<Player> saved = playerRepository.findById(createdPlayer.getId());
        assertThat(saved).isPresent();
        assertThat(saved.get().getName()).isEqualTo(request.getName());

        // Retrieve player
        mockMvc.perform(get("/api/players/" + createdPlayer.getId()))
            .andExpect(status().isOk())
            .andExpect(jsonPath("$.id").value(createdPlayer.getId()))
            .andExpect(jsonPath("$.name").value(request.getName()));
    }
}
```

## Frontend Testing

### Unit Testing with Vitest

#### Component Testing

```typescript
// PlayerCard.spec.ts
import { describe, it, expect, vi, beforeEach } from 'vitest';
import { mount } from '@vue/test-utils';
import { createTestingPinia } from '@pinia/testing';
import PlayerCard from '@/components/PlayerCard.vue';
import { usePlayerStore } from '@/stores/player';

describe('PlayerCard', () => {
  let wrapper: any;
  let playerStore: any;

  const mockPlayer = {
    id: 1,
    name: 'Test Player',
    rating: 75,
    skills: [
      { skill: 'Speed', rating: 80 },
      { skill: 'Strength', rating: 70 }
    ]
  };

  beforeEach(() => {
    wrapper = mount(PlayerCard, {
      props: {
        player: mockPlayer,
        editable: true
      },
      global: {
        plugins: [
          createTestingPinia({
            createSpy: vi.fn,
            stubActions: false
          })
        ]
      }
    });

    playerStore = usePlayerStore();
  });

  it('should display player information', () => {
    expect(wrapper.text()).toContain('Test Player');
    expect(wrapper.text()).toContain('75');
  });

  it('should emit edit event when edit button clicked', async () => {
    await wrapper.find('[data-testid="edit-button"]').trigger('click');

    expect(wrapper.emitted('edit')).toBeTruthy();
    expect(wrapper.emitted('edit')[0]).toEqual([mockPlayer]);
  });

  it('should call store delete method when confirmed', async () => {
    const deleteSpy = vi.spyOn(playerStore, 'deletePlayer');

    await wrapper.find('[data-testid="delete-button"]').trigger('click');
    await wrapper.find('[data-testid="confirm-delete"]').trigger('click');

    expect(deleteSpy).toHaveBeenCalledWith(mockPlayer.id);
  });
});
```

#### Store Testing

```typescript
// stores/player.spec.ts
import { describe, it, expect, beforeEach, vi } from 'vitest';
import { setActivePinia, createPinia } from 'pinia';
import { usePlayerStore } from '@/stores/player';
import * as playerApi from '@/api/services/playerApi';

vi.mock('@/api/services/playerApi');

describe('Player Store', () => {
  let store: ReturnType<typeof usePlayerStore>;

  beforeEach(() => {
    setActivePinia(createPinia());
    store = usePlayerStore();
  });

  it('should fetch players successfully', async () => {
    const mockPlayers = [
      { id: 1, name: 'Player 1', rating: 75 },
      { id: 2, name: 'Player 2', rating: 80 }
    ];

    vi.mocked(playerApi.getAllPlayers).mockResolvedValue({
      players: mockPlayers,
      total: 2
    });

    await store.fetchPlayers();

    expect(store.selectablePlayers).toEqual(mockPlayers);
    expect(store.isLoading).toBe(false);
    expect(store.error).toBe(null);
  });

  it('should handle fetch error', async () => {
    const error = new Error('Network error');
    vi.mocked(playerApi.getAllPlayers).mockRejectedValue(error);

    await store.fetchPlayers();

    expect(store.selectablePlayers).toEqual([]);
    expect(store.isLoading).toBe(false);
    expect(store.error).toBe('Failed to fetch players');
  });
});
```

### E2E Testing with Cypress

#### Test Setup

```typescript
// cypress/support/commands.ts
Cypress.Commands.add('login', (username: string, password: string) => {
  cy.visit('/login');
  cy.get('[data-testid="username-input"]').type(username);
  cy.get('[data-testid="password-input"]').type(password);
  cy.get('[data-testid="login-button"]').click();
  cy.url().should('not.include', '/login');
});
```

#### E2E Test Scenarios

```typescript
// cypress/e2e/player-management.cy.ts
describe('Player Management', () => {
  beforeEach(() => {
    cy.visit('/');
    cy.login('admin', 'admin');
  });

  it('should create a new player', () => {
    cy.visit('/players/create');
    cy.get('[data-testid="player-name"]').type('New Player');
    cy.get('[data-testid="player-rating"]').type('75');
    cy.get('[data-testid="add-skill"]').click();
    cy.get('[data-testid="skill-name-0"]').type('Speed');
    cy.get('[data-testid="skill-rating-0"]').type('80');
    cy.get('[data-testid="save-player"]').click();
    cy.url().should('include', '/players');
    cy.contains('New Player').should('be.visible');
  });
});
```

## TDD Methodology

### Human-in-the-Loop TDD

**End-of-Phase Confirmation**
```
Red Phase Complete:
- Test Activated: "should return sum for two numbers"
- Prediction: Runtime assertion error (Expected: 3, Received: 0)
- Result: Test fails as expected

Red phase complete. Should I proceed to Green phase?
```

## Testing Best Practices

### Test Naming Conventions

**Backend (Java)**: `should_return_player_when_valid_id_provided`
**Frontend (TypeScript)**: `'should display player information correctly'`

### Test Data Builders

```java
public class TestDataBuilder {
    public static Player createPlayer() {
        return Player.builder()
            .id(1L).name("Test Player").rating(75)
            .skills(Arrays.asList(createSkill("Speed", 80)))
            .build();
    }
}
```

```typescript
export const createMockPlayer = (overrides = {}): Player => ({
  id: 1, name: 'Test Player', rating: 75, skills: [],
  groupId: 1, userId: 1, ...overrides
});
```

### Coverage Requirements

- **Unit Tests**: Minimum 80% line coverage
- **Integration Tests**: Critical user flows
- **E2E Tests**: Happy paths and edge cases
- **Security Tests**: All authorization scenarios

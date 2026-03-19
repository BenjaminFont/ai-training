---
paths:
  - "team-finder-backend/src/main/java/com/teamfinder/backend/domain/model/**"
  - "team-finder-backend/src/main/java/com/teamfinder/backend/domain/repository/**"
  - "team-finder-backend/src/main/resources/db/migration/**"
  - "team-finder-frontend/src/types.*"
---

# Data Models & Database

## Overview

- **Development**: H2 in-memory database
- **Production**: PostgreSQL
- **Migrations**: Flyway for version control
- **ORM**: Spring Data JPA with Hibernate

## Core Entity Relationships

```
AppUser (1:n) --> Player (1:n) --> PlayerSkill
AppUser (n:n) <-> UserGroup
Player (n:1) --> UserGroup
```

## Entity Definitions

### AppUser Entity

```java
@Entity
@Table(name = "app_user")
@Data @Builder @NoArgsConstructor @AllArgsConstructor
public class AppUser {
    @Id @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    @Column(unique = true, nullable = false, length = 50)
    private String username;

    @Column(nullable = false) @JsonIgnore
    private String password;

    @Enumerated(EnumType.STRING)
    @Column(nullable = false, length = 20)
    private UserRole role;

    @ManyToMany(fetch = FetchType.LAZY)
    @JoinTable(name = "user_groups_members",
        joinColumns = @JoinColumn(name = "user_id"),
        inverseJoinColumns = @JoinColumn(name = "group_id"))
    private Set<UserGroup> groups = new HashSet<>();

    @ManyToMany(fetch = FetchType.LAZY)
    @JoinTable(name = "user_groups_admins",
        joinColumns = @JoinColumn(name = "user_id"),
        inverseJoinColumns = @JoinColumn(name = "group_id"))
    private Set<UserGroup> adminGroups = new HashSet<>();

    @OneToMany(mappedBy = "user", cascade = CascadeType.ALL, orphanRemoval = true)
    private List<Player> players = new ArrayList<>();
}
```

### Player Entity

```java
@Entity
@Table(name = "player")
@Data @Builder @NoArgsConstructor @AllArgsConstructor
public class Player {
    @Id @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    @Column(nullable = false, length = 100)
    private String name;

    @Column(nullable = false)
    private Integer rating;

    @Column(name = "phone_number", length = 20)
    private String phoneNumber;

    @Column(name = "img_src", length = 500)
    private String imgSrc;

    @ManyToOne(fetch = FetchType.LAZY)
    @JoinColumn(name = "user_id", nullable = false)
    private AppUser user;

    @ManyToOne(fetch = FetchType.LAZY)
    @JoinColumn(name = "group_id")
    private UserGroup group;

    @OneToMany(mappedBy = "player", cascade = CascadeType.ALL, orphanRemoval = true)
    @Builder.Default
    private List<PlayerSkill> skills = new ArrayList<>();
}
```

### PlayerSkill Entity

```java
@Entity
@Table(name = "player_skill")
@Data @Builder @NoArgsConstructor @AllArgsConstructor
public class PlayerSkill {
    @Id @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    @Column(nullable = false, length = 50)
    private String skill;

    @Column(nullable = false) @Min(0) @Max(100)
    private Integer rating;

    @ManyToOne(fetch = FetchType.LAZY)
    @JoinColumn(name = "player_id", nullable = false) @JsonIgnore
    private Player player;
}
```

## Business Rules & Constraints

- Each player must have exactly one owner (user)
- A player can belong to at most one group
- Players can only be assigned to groups where the owner is a member
- **SYSTEM_ADMIN**: Unlimited players per group
- **GROUP_ADMIN**: Unlimited players in administered groups
- **USER**: Maximum one player per group

## Repository Layer

```java
@Repository
public interface PlayerRepository extends JpaRepository<Player, Long> {
    List<Player> findByGroupId(Long groupId);
    List<Player> findByUserId(Long userId);
    Optional<Player> findByIdAndUserId(Long id, Long userId);
    boolean existsByNameAndGroupId(String name, Long groupId);
    boolean existsByUserIdAndGroupId(Long userId, Long groupId);

    @Query("SELECT p FROM Player p LEFT JOIN FETCH p.skills WHERE p.id = :id")
    Optional<Player> findByIdWithSkills(@Param("id") Long id);

    @Query("SELECT DISTINCT p FROM Player p LEFT JOIN FETCH p.skills WHERE p.group.id = :groupId")
    List<Player> findByGroupIdWithSkills(@Param("groupId") Long groupId);
}
```

## Data Access Patterns

### Lazy Loading - Use fetch joins to avoid N+1 problem

```java
@Service
@Transactional(readOnly = true)
public class PlayerService {
    public PlayerDto getPlayerWithSkills(Long id) {
        Player player = playerRepository.findByIdWithSkills(id)
            .orElseThrow(() -> new EntityNotFoundException("Player", id));
        return mapToDto(player);
    }
}
```

### Projections for Read-Only Data

```java
public interface PlayerProjection {
    Long getId();
    String getName();
    Integer getRating();
}
```

### Pagination

```java
Page<Player> findByGroupId(Long groupId, Pageable pageable);
// Usage: PageRequest.of(0, 20, Sort.by("name"))
```

## Flyway Migrations

**Location**: `src/main/resources/db/migration/`
**Naming**: `V{version}__{description}.sql`

Example:
```sql
CREATE TABLE player (
    id BIGSERIAL PRIMARY KEY,
    name VARCHAR(100) NOT NULL,
    rating INTEGER NOT NULL,
    phone_number VARCHAR(20),
    img_src VARCHAR(500),
    user_id BIGINT NOT NULL,
    group_id BIGINT,
    FOREIGN KEY (user_id) REFERENCES app_user(id) ON DELETE CASCADE,
    FOREIGN KEY (group_id) REFERENCES user_group(id) ON DELETE SET NULL
);

CREATE INDEX idx_player_user_id ON player(user_id);
CREATE INDEX idx_player_group_id ON player(group_id);
```

## Database Configuration

**H2 Dev**: `jdbc:h2:mem:teamfinder` | user: `sa` | pass: `password` | Console: http://localhost:9000/h2-console
**PostgreSQL Prod**: Via `DATABASE_URL` environment variable, HikariCP connection pool (max 10)

**Entity Pattern**: `@Entity` + Lombok annotations + `FetchType.LAZY` for all relationships

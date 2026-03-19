---
paths:
  - "team-finder-backend/src/main/java/com/teamfinder/backend/security/**"
  - "team-finder-backend/src/main/java/com/teamfinder/backend/controller/AuthController.java"
  - "team-finder-backend/src/main/java/com/teamfinder/backend/config/SecurityConfig.java"
  - "team-finder-backend/src/main/java/com/teamfinder/backend/config/CorsConfig.java"
  - "team-finder-frontend/src/stores/admin.*"
  - "team-finder-frontend/src/router/**"
---

# Security & Authentication

## Overview

Team Finder implements a comprehensive security architecture with:
- **JWT-based authentication** for stateless session management
- **Role-Based Access Control (RBAC)** with three-tier permission system
- **Group-based permissions** for multi-tenant data isolation
- **Method-level security** using Spring Security annotations

## Authentication System

### JWT Token Structure

```json
{
  "payload": {
    "sub": "user@example.com",
    "userId": 123,
    "role": "GROUP_ADMIN",
    "groups": [1, 2, 3],
    "iat": 1699564800,
    "exp": 1699651200
  }
}
```

### Token Generation

```java
@Service
@RequiredArgsConstructor
public class JwtTokenProvider {
    @Value("${jwt.secret}")
    private String jwtSecret;

    @Value("${jwt.expiration}")
    private int jwtExpirationInMs;

    public String createToken(Authentication authentication) {
        UserPrincipal userPrincipal = (UserPrincipal) authentication.getPrincipal();
        Date expiryDate = new Date(System.currentTimeMillis() + jwtExpirationInMs);

        return Jwts.builder()
            .setSubject(userPrincipal.getUsername())
            .claim("userId", userPrincipal.getId())
            .claim("role", userPrincipal.getRole())
            .claim("groups", userPrincipal.getGroupIds())
            .setIssuedAt(new Date())
            .setExpiration(expiryDate)
            .signWith(SignatureAlgorithm.HS512, jwtSecret)
            .compact();
    }
}
```

### JWT Filter Chain

```java
@Component
public class JwtAuthenticationFilter extends OncePerRequestFilter {
    @Override
    protected void doFilterInternal(HttpServletRequest request,
                                   HttpServletResponse response,
                                   FilterChain filterChain) throws ServletException, IOException {
        try {
            String jwt = getJwtFromRequest(request);
            if (StringUtils.hasText(jwt) && tokenProvider.validateToken(jwt)) {
                String username = tokenProvider.getUsernameFromJWT(jwt);
                UserDetails userDetails = customUserDetailsService.loadUserByUsername(username);
                UsernamePasswordAuthenticationToken authentication =
                    new UsernamePasswordAuthenticationToken(userDetails, null, userDetails.getAuthorities());
                SecurityContextHolder.getContext().setAuthentication(authentication);
            }
        } catch (Exception ex) {
            logger.error("Could not set user authentication in security context", ex);
        }
        filterChain.doFilter(request, response);
    }

    private String getJwtFromRequest(HttpServletRequest request) {
        String bearerToken = request.getHeader("Authorization");
        if (StringUtils.hasText(bearerToken) && bearerToken.startsWith("Bearer ")) {
            return bearerToken.substring(7);
        }
        return null;
    }
}
```

## Authorization System

### Three-Tier Role System

1. **SYSTEM_ADMIN**: Full access to all resources, no restrictions
2. **GROUP_ADMIN**: Administrative rights to specific groups, multiple players per group
3. **USER**: Basic access, one player per group, can manage own players only

### Role Hierarchy

```java
@Configuration
public class SecurityConfig {
    @Bean
    public RoleHierarchy roleHierarchy() {
        RoleHierarchyImpl roleHierarchy = new RoleHierarchyImpl();
        roleHierarchy.setHierarchy("ROLE_SYSTEM_ADMIN > ROLE_GROUP_ADMIN > ROLE_USER");
        return roleHierarchy;
    }
}
```

### Method-Level Security

```java
@RestController
@RequestMapping("/api/players")
public class PlayerController {

    @GetMapping
    public ResponseEntity<List<PlayerDto>> getAllPlayers() {
        return ResponseEntity.ok(playerService.getAllPlayers());
    }

    @PostMapping
    @PreAuthorize("isAuthenticated()")
    public ResponseEntity<PlayerDto> createPlayer(@RequestBody CreatePlayerRequest request) {
        return ResponseEntity.status(HttpStatus.CREATED).body(playerService.createPlayer(request));
    }

    @PutMapping("/{id}")
    @PreAuthorize("@playerService.canModifyPlayer(#id)")
    public ResponseEntity<PlayerDto> updatePlayer(@PathVariable Long id, @RequestBody UpdatePlayerRequest request) {
        return ResponseEntity.ok(playerService.updatePlayer(id, request));
    }

    @DeleteMapping("/{id}")
    @PreAuthorize("hasRole('SYSTEM_ADMIN')")
    public ResponseEntity<Void> deletePlayer(@PathVariable Long id) {
        playerService.deletePlayer(id);
        return ResponseEntity.noContent().build();
    }
}
```

### Service-Level Permission Checking

```java
@Service
@RequiredArgsConstructor
public class PlayerService {
    public PlayerDto createPlayer(CreatePlayerRequest request) {
        AppUser currentUser = securityUtils.getCurrentUser()
            .orElseThrow(() -> new AccessDeniedException("User not authenticated"));

        // Check group membership
        if (request.getGroupId() != null) {
            if (!currentUser.getGroups().stream()
                    .anyMatch(g -> g.getId().equals(request.getGroupId()))) {
                throw new AccessDeniedException("User not member of specified group");
            }
            // Check player limit for non-admins
            if (currentUser.getRole() == UserRole.USER) {
                if (playerRepository.existsByUserIdAndGroupId(currentUser.getId(), request.getGroupId())) {
                    throw new BusinessException("Users can only have one player per group");
                }
            }
        }
        // ... create player
    }

    public boolean canModifyPlayer(Long playerId) {
        AppUser user = securityUtils.getCurrentUser().orElse(null);
        if (user == null) return false;
        Player player = playerRepository.findById(playerId).orElse(null);
        if (player == null) return false;

        if (user.getRole() == UserRole.SYSTEM_ADMIN) return true;
        if (user.getRole() == UserRole.GROUP_ADMIN && player.getGroup() != null)
            return player.getGroup().getAdmins().contains(user);
        return player.getUser().getId().equals(user.getId());
    }
}
```

## Frontend Authentication

### Admin Store

```typescript
export const useAdminStore = defineStore("admin", () => {
  const isLoggedIn = ref(false);
  const userRole = ref<string | null>(null);
  const token = ref<string | null>(null);
  const userId = ref<number | null>(null);
  const groups = ref<number[]>([]);

  async function login(username: string, password: string): Promise<boolean> {
    try {
      const response = await authApi.login({ username, password });
      token.value = response.accessToken;
      userRole.value = response.role;
      userId.value = response.userId;
      groups.value = response.groups;
      isLoggedIn.value = true;
      localStorage.setItem('jwt-token', response.accessToken);
      axios.defaults.headers.common['Authorization'] = `Bearer ${response.accessToken}`;
      return true;
    } catch (error) {
      return false;
    }
  }

  function logout() {
    isLoggedIn.value = false;
    token.value = null;
    localStorage.removeItem('jwt-token');
    delete axios.defaults.headers.common['Authorization'];
    router.push('/');
  }

  return {
    isLoggedIn: readonly(isLoggedIn),
    userRole: readonly(userRole),
    login, logout,
    hasRole: (role: string) => userRole.value === role,
    belongsToGroup: (groupId: number) => groups.value.includes(groupId)
  };
});
```

### Navigation Guards

```javascript
router.beforeEach((to, from, next) => {
  const adminStore = useAdminStore();

  if (to.matched.some(record => record.meta.requiresAuth)) {
    if (!adminStore.isLoggedIn) {
      next({ path: '/login', query: { redirect: to.fullPath } });
      return;
    }
  }

  if (to.meta.requiresRole) {
    if (!adminStore.hasRole(to.meta.requiresRole)) {
      next({ path: '/unauthorized' });
      return;
    }
  }

  next();
});
```

### Conditional UI Rendering

```vue
<div v-if="canEditPlayer" class="actions">
  <button @click="editPlayer">Edit</button>
  <button v-if="isAdmin" @click="deletePlayer">Delete</button>
</div>

<script setup lang="ts">
const canEditPlayer = computed(() => {
  if (adminStore.hasRole('SYSTEM_ADMIN')) return true;
  if (adminStore.hasRole('GROUP_ADMIN') && adminStore.belongsToGroup(props.player.groupId)) return true;
  return props.player.userId === adminStore.userId;
});
</script>
```

## Security Best Practices

- Use `@Valid` for input validation on all request DTOs
- Always get user via `SecurityUtils.getCurrentUser()`
- Apply `@PreAuthorize` at controller method level
- Never log passwords or JWT tokens
- Use environment variables for secrets (JWT_SECRET, etc.)
- BCrypt with 12 rounds for password hashing
- CORS: only allow specific frontend origins
- Security headers: frame-options deny, XSS protection, HSTS, CSP
- Rate limiting on authentication endpoints
- Failed login tracking with account lockout after 5 attempts

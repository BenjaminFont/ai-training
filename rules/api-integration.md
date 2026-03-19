---
paths:
  - "team-finder-backend/src/main/java/com/teamfinder/backend/controller/**"
  - "team-finder-backend/src/main/java/com/teamfinder/backend/domain/dto/**"
  - "team-finder-backend/src/main/java/com/teamfinder/backend/exception/**"
  - "team-finder-frontend/src/api.*"
  - "team-finder-frontend/src/api/**"
---

# API Integration

## RESTful Resource Conventions

```
GET    /api/{resource}          -> List all resources
GET    /api/{resource}/{id}     -> Get single resource
POST   /api/{resource}          -> Create new resource
PUT    /api/{resource}/{id}     -> Update resource
DELETE /api/{resource}/{id}     -> Delete resource
```

## Backend Controller Structure

```java
@RestController
@RequestMapping("/api/players")
@RequiredArgsConstructor
public class PlayerController {

    private final PlayerService playerService;

    @GetMapping
    public ResponseEntity<List<PlayerDto>> getAllPlayers(
            @RequestParam(required = false) Long groupId,
            @RequestParam(defaultValue = "0") int page,
            @RequestParam(defaultValue = "20") int size,
            @RequestParam(defaultValue = "name") String sortBy) {
        Pageable pageable = PageRequest.of(page, size, Sort.by(sortBy));
        Page<PlayerDto> players = playerService.getPlayers(groupId, pageable);
        return ResponseEntity.ok()
            .header("X-Total-Count", String.valueOf(players.getTotalElements()))
            .body(players.getContent());
    }

    @PostMapping
    @PreAuthorize("isAuthenticated()")
    public ResponseEntity<PlayerDto> createPlayer(@Valid @RequestBody CreatePlayerRequest request) {
        PlayerDto created = playerService.createPlayer(request);
        URI location = ServletUriComponentsBuilder
            .fromCurrentRequest().path("/{id}")
            .buildAndExpand(created.getId()).toUri();
        return ResponseEntity.created(location).body(created);
    }
}
```

## Request/Response DTOs

```java
// Request DTO with validation
@Data @Builder @NoArgsConstructor @AllArgsConstructor
public class CreatePlayerRequest {
    @NotBlank(message = "Name is required")
    @Size(max = 100)
    private String name;

    @NotNull @Min(0) @Max(100)
    private Integer rating;

    private String phoneNumber;
    private String imgSrc;

    @Valid @NotEmpty @Size(max = 10)
    private List<SkillDto> skills;

    private Long groupId;
}

// Response DTO
@Data @Builder
@JsonInclude(JsonInclude.Include.NON_NULL)
public class PlayerDto {
    private Long id;
    private String name;
    private Integer rating;
    private String phoneNumber;
    private String imgSrc;
    private List<SkillDto> skills;
    private Long userId;
    private Long groupId;
}
```

## Error Response Structure

```java
@Data @Builder @AllArgsConstructor
public class ErrorResponse {
    private String timestamp;
    private int status;
    private String error;
    private String message;
    private String path;
    private Map<String, String> validationErrors;
}
```

## Global Exception Handler

```java
@ControllerAdvice
public class GlobalExceptionHandler {
    @ExceptionHandler(EntityNotFoundException.class)
    public ResponseEntity<ErrorResponse> handleEntityNotFound(EntityNotFoundException ex, HttpServletRequest request) {
        return ResponseEntity.status(HttpStatus.NOT_FOUND)
            .body(ErrorResponse.of(HttpStatus.NOT_FOUND, ex.getMessage(), request.getRequestURI()));
    }

    @ExceptionHandler(MethodArgumentNotValidException.class)
    public ResponseEntity<ErrorResponse> handleValidationExceptions(MethodArgumentNotValidException ex, HttpServletRequest request) {
        Map<String, String> errors = new HashMap<>();
        ex.getBindingResult().getFieldErrors().forEach(error ->
            errors.put(error.getField(), error.getDefaultMessage()));
        ErrorResponse errorResponse = ErrorResponse.of(HttpStatus.BAD_REQUEST, "Validation failed", request.getRequestURI());
        errorResponse.setValidationErrors(errors);
        return ResponseEntity.status(HttpStatus.BAD_REQUEST).body(errorResponse);
    }

    @ExceptionHandler(AccessDeniedException.class)
    public ResponseEntity<ErrorResponse> handleAccessDenied(AccessDeniedException ex, HttpServletRequest request) {
        return ResponseEntity.status(HttpStatus.FORBIDDEN)
            .body(ErrorResponse.of(HttpStatus.FORBIDDEN, "Access denied", request.getRequestURI()));
    }
}
```

## Frontend API Client

### Axios Configuration

```typescript
const API_BASE_URL = process.env.NODE_ENV === 'production'
  ? 'https://api.teamfinder.com'
  : 'http://localhost:9000';

const api = axios.create({
  baseURL: `${API_BASE_URL}/api`,
  timeout: 10000,
  headers: { 'Content-Type': 'application/json' },
});

// Request interceptor - add JWT token
api.interceptors.request.use((config) => {
  const token = localStorage.getItem('jwt-token');
  if (token) {
    config.headers.Authorization = `Bearer ${token}`;
  }
  return config;
});

// Response interceptor - handle 401/403
api.interceptors.response.use(
  (response) => response,
  async (error) => {
    if (error.response?.status === 401) {
      adminStore.logout();
      router.push('/login');
    }
    if (error.response?.status === 403) {
      router.push('/unauthorized');
    }
    return Promise.reject(error);
  }
);
```

### API Service Layer

```typescript
export const playerApi = {
  async getAllPlayers(params?: { groupId?: number; page?: number; size?: number }): Promise<{ players: Player[]; total: number }> {
    const response = await api.get<Player[]>('/players', { params });
    return { players: response.data, total: parseInt(response.headers['x-total-count'] || '0') };
  },

  async createPlayer(player: CreatePlayerRequest): Promise<Player> {
    const { data } = await api.post<Player>('/players', player);
    return data;
  },

  async uploadProfileImage(file: File): Promise<string> {
    const formData = new FormData();
    formData.append('file', file);
    const { data } = await api.post<{ imageUrl: string }>('/players/upload-image', formData, {
      headers: { 'Content-Type': 'multipart/form-data' },
    });
    return data.imageUrl;
  },

  async updatePlayer(id: number, player: UpdatePlayerRequest): Promise<Player> {
    const { data } = await api.put<Player>(`/players/${id}`, player);
    return data;
  },

  async deletePlayer(id: number): Promise<void> {
    await api.delete(`/players/${id}`);
  },
};
```

## S3 Upload Flow

```
Client -> Frontend -> Backend -> AWS S3
  ^                               |
  +-------- S3 URL <--------------+
```

Backend validates file (image only, max 5MB), generates UUID filename, uploads to S3, returns public URL.

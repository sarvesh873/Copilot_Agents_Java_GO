---
description: 'Principal Spring Boot Code Reviewer. Enforces SOLID, Java 17/21 idioms, and Spring Boot best practices.'
applyTo: '**/*.java'
---

# 🕵️‍♂️ Master Spring Boot Code Review Instructions

Comprehensive code review guidelines for GitHub Copilot, specifically tailored for our Java and Spring Boot ecosystem. These instructions enforce strict architectural boundaries, modern Java idioms, and robust testing standards.

## Review Language

When performing a code review, respond in **English**. Maintain a professional, constructive, and highly technical tone.

## Review Priorities

When performing a code review, prioritize issues in the following order:

### 🔴 CRITICAL (Block merge)
- **Dependency Injection**: Any use of `@Autowired` on fields. (Must use Constructor Injection).
- **Security**: SQL injection risks, exposed secrets, missing `@PreAuthorize` on sensitive endpoints.
- **Transaction Leaks**: Missing, misused, or overly broad `@Transactional` boundaries.
- **Data Leaks**: Returning raw JPA `@Entity` classes directly from Controllers (must use DTOs/Records).

### 🟡 IMPORTANT (Requires discussion)
- **Code Quality**: Severe violations of SOLID principles, "God Classes", or methods exceeding 15-20 lines.
- **Test Coverage**: Missing JUnit 5 tests for critical paths, edge cases, or exception handling.
- **Performance**: N+1 query problems in Spring Data JPA.
- **Context Bloat**: Using `@SpringBootTest` when a lightweight slice (`@WebMvcTest`, `@DataJpaTest`) is sufficient.

### 🟢 SUGGESTION (Non-blocking improvements)
- **Modern Java Idioms**: Opportunities to use `Records`, `Switch Expressions`, `Text Blocks`, or `Optional<T>`.
- **Readability**: Poor naming or complex nested logic that could be simplified with early returns.
- **Best Practices**: Converting generic JUnit assertions to fluent AssertJ assertions (`assertThat`).

## General Review Principles

1. **Be specific**: Reference exact lines, files, and provide concrete Java/Spring examples.
2. **Provide context**: Explain WHY something is an issue (e.g., "Field injection makes unit testing difficult without a Spring context").
3. **Suggest solutions**: Show the refactored code using our established tech stack.
4. **Group related comments**: Consolidate feedback rather than spamming multiple comments on the same block.

## Code Quality Standards (Strict Enforcement)

### 1. Architecture & The SOLID Gates
Aggressively review the codebase against these core principles:
- **Single Responsibility (SRP)**: Flag methods that exceed 15 lines or have a Cyclomatic Complexity greater than 10. Flag "God Classes" that handle HTTP mapping, business logic, and database orchestration all in one place.
- **Open/Closed (OCP)**: Flag large `switch` blocks or chained `if-else` statements. Suggest refactoring to Strategy Patterns or utilizing Polymorphism.
- **Dependency Inversion (DIP)**: Enforce Constructor Injection. Depend on abstractions (Interfaces), not concrete implementations.
- **Interface Segregation (ISP)**: Ensure interfaces are lean. Flag massive interfaces that force classes to implement unused methods.

### 2. Clean Code & Modern Java
- **Data Transfer**: Enforce the use of **Java Records** for DTOs and Request/Response bodies.
- **Null Safety**: Reject methods returning `null`. Enforce the use of `Optional<T>` to prevent `NullPointerException`.
- **Logging Hygiene**: Reject any use of `System.out.println`. Enforce SLF4J structured logging (`log.info`, `log.error`).
- **Simplicity**: Flag duplicated logic (DRY) and over-engineered abstractions (YAGNI/KISS).

### Examples
```java
// ❌ BAD: Field injection and raw entity exposure
@RestController
public class UserController {
    @Autowired
    private UserRepository repository;

    @GetMapping("/users/{id}")
    public User getUser(@PathVariable Long id) { 
        return repository.findById(id).orElse(null); 
    }
}

// ✅ GOOD: Constructor injection, DTO mapping, and proper exception handling
@RestController
@RequiredArgsConstructor
public class UserController {
    private final UserService userService;

    @GetMapping("/users/{id}")
    public ResponseEntity<UserResponse> getUser(@PathVariable Long id) {
        return ResponseEntity.ok(userService.getUserById(id));
    }
}
```

### Error Handling
- Never use `try-catch` blocks in Controllers to handle business logic.
- Rely on global `@RestControllerAdvice` to map domain exceptions to HTTP responses.
- Fail fast: validate inputs early using `jakarta.validation` (`@Valid`, `@NotNull`).

```java
// ❌ BAD: Generic exception and silent failure
public void processPayment(Long orderId) {
    try {
        Order order = repository.findById(orderId).get();
        gateway.charge(order);
    } catch (Exception e) {
        log.error("Error processing payment");
    }
}

// ✅ GOOD: Specific exceptions mapped to ControllerAdvice
public void processPayment(Long orderId) {
    Order order = repository.findById(orderId)
        .orElseThrow(() -> new ResourceNotFoundException("Order not found: " + orderId));
        
    if (!gateway.charge(order)) {
        throw new PaymentProcessingException("Failed to charge order: " + orderId);
    }
}
```

## Performance Considerations (Spring Data JPA)

When reviewing data access layers, aggressively check for performance bottlenecks.

- **N+1 Queries**: Look for loops iterating over lazily-loaded relationships.
- **Projections**: Suggest interface or record projections instead of fetching entire entities if only a few columns are needed.

### Examples
```java
// ❌ BAD: Triggers N+1 query problem
List<Author> authors = authorRepository.findAll();
for (Author author : authors) {
    log.info("Books: {}", author.getBooks().size()); // Triggers a new query per author!
}

// ✅ GOOD: Use @EntityGraph or JOIN FETCH in the Repository
@Query("SELECT a FROM Author a JOIN FETCH a.books")
List<Author> findAllWithBooks();
```

## Testing Standards

Verify test quality strictly using our stack (JUnit 5, Mockito, AssertJ).

- **Naming**: Use `methodName_should_expectedBehavior_when_scenario`.
- **Structure**: Clear Arrange-Act-Assert (Given-When-Then) pattern.
- **Isolation**: Use `@Mock` for external dependencies and `@InjectMocks` for the target class.
- **Assertions**: Always favor AssertJ over standard JUnit assertions.

### Examples
```java
// ❌ BAD: Vague name, no isolation, standard assertions
@Test
void testDiscount() {
    Order order = new Order(100);
    assertEquals(90, service.applyDiscount(order));
}

// ✅ GOOD: Descriptive, strict isolation, fluent assertions
@Test
void applyDiscount_shouldReturnDiscountedAmount_whenOrderQualifies() {
    // Arrange
    Order order = new Order(100);
    when(discountPolicy.isEligible(order)).thenReturn(true);
    when(discountPolicy.calculate(order)).thenReturn(10.0);

    // Act
    BigDecimal result = paymentService.applyDiscount(order);

    // Assert
    assertThat(result).isEqualByComparingTo(BigDecimal.valueOf(90));
    verify(discountPolicy).calculate(order);
}
```

## Comment Format Template

Use this format for generating review comments:

```markdown
**[PRIORITY] Category: Brief title**

Detailed description of the issue or suggestion.

**Why this matters:**
Explanation of the impact (e.g., performance, testability, security).

**Suggested fix:**
[Java/Spring code example applying the fix]

**Reference:** [Relevant SOLID principle, Design Pattern, or Spring best practice]
```

Reference: [Relevant SOLID principle, Design Pattern, or Spring best practice]


## Review Checklist

Systematically verify the following during the review:

### Architecture & Spring Boot
- [ ] No `@Autowired` on fields (Constructor injection used).
- [ ] Controllers return Records/DTOs, not `@Entity` classes.
- [ ] Business logic resides in `@Service`, not `@RestController`.
- [ ] Exceptions are handled globally via `@RestControllerAdvice`.

### Code Quality & Java 17/21
- [ ] Methods are focused and small (< 15 lines) with CC < 10.
- [ ] Java `Record` types used for immutable data/DTOs.
- [ ] `Optional<T>` used for null safety.
- [ ] `System.out.println` replaced with SLF4J.

### Performance & Security
- [ ] No N+1 query risks in JPA repositories.
- [ ] `@Transactional` boundaries applied correctly at the Service layer.

### Testing
- [ ] New logic covered by JUnit 5 tests.
- [ ] Edge cases and exceptions explicitly tested using `assertThrows`.
- [ ] Assertions use AssertJ (`assertThat`).

## Project Context

Ensure all code reviews align with this specific environment:

- **Tech Stack**: Java 17/21, Spring Boot 3.x
- **Build Tool**: Maven
- **Datastore**: RDBMS (PostgreSQL/MySQL) / Elasticsearch
- **Testing**: JUnit 5, Mockito, AssertJ
- **Architecture**: Layered Architecture (Web -> Service -> Persistence)

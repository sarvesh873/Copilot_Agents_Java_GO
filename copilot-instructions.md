# Engineering defaults: build the least, not the most

**Step 0 — check the mode.** Read `.github/copilot-mode.md`; missing or unparseable = `full`.

| Mode | Behavior |
|------|----------|
| `lite` | Build the requested solution; if a simpler stdlib/native/existing-dependency alternative exists, note it in one line, don't block on it. |
| `full` (default) | Apply the ladder below; take the lowest rung that resolves the task. Skipping a more robust approach gets a one-line comment naming it. |
| `ultra` | Refuse anything above the lowest working rung unless justified. Push back on the requirement itself if a smaller change covers the actual need — "might need this later" doesn't count. |
| `off` | Ignore the ladder below. Behave as a normal coding assistant. |

Switch: `/lazy-mode` in chat, or edit `.github/copilot-mode.md` directly. Effective next response; both files re-read every turn.

## The ladder

Before new code, work down this list; stop at the first step that resolves the task:

1. **Necessary now?** Speculative ("might want this later") → skip it.
2. **Already exists?** Check existing `@Service`/`@Repository`/mapper/util classes across the codebase before building new ones — a duplicate `XService`/`XUtil` a few packages over is the most common waste here.
3. **Java standard library?** `java.time`, `java.util.*`, `Optional`, Streams — prefer over a new dependency or hand-rolled utility.
4. **Spring/Spring Boot already solve this?**
   - Validation → Bean Validation (`@Valid`, `@NotNull`, `@Size`, custom `@Constraint`) on the DTO, not manual if-checks.
   - Error responses → one `@RestControllerAdvice` / `@ExceptionHandler`, not try-catch per endpoint.
   - Config → `@ConfigurationProperties` + `application.yml`, not hand-parsed properties.
   - Data access → a Spring Data JPA derived query or `@Query`, not a new `JdbcTemplate` call or DAO class.
   - Entity/DTO mapping → MapStruct/ModelMapper if already a dependency, not a new manual mapper.
   - Cross-cutting concerns → an existing `@Aspect`, filter, or interceptor, not duplicated per-method logic.
5. **Already a dependency in `pom.xml`/`build.gradle`?** Inspect `pom.xml` / `build.gradle` or run `mvn dependency:tree` before assuming you need a new dependency.
6. **One line / one small method?** Do that instead of a new class, interface, or abstraction layer.
7. Only after 1–6: write the smallest amount of new code that correctly solves the task.

## Never cut, regardless of mode or ladder

- `@Valid` / `@Validated` at controller and service boundaries.
- Transaction boundaries (`@Transactional`) and rollback semantics.
- Exception handling that distinguishes 4xx from 5xx.
- **Customer data**: no logging of PII/sensitive fields (check logging config first), no widening of a repository query or DTO's exposed fields beyond what the endpoint needs, never bypass an existing masking/encryption/redaction utility.
- Spring Security config — authentication, authorization, CORS/CSRF.
- Accessibility on server-rendered (Thymeleaf/JSP) views.

## File reading discipline

Every file read is input tokens that persist in context for the rest of the session. Before opening any file:

1. Query first: run targeted searches (symbol lookup, method search, route mappings) for specific classes before opening files speculatively.
2. Check interfaces and signatures before reading full implementation files.
3. Don't re-read a file already open in this session's context. If you need a specific line range, reference it directly.
4. Read the minimum needed: a class signature, a method body, a config block — not the entire file unless the task genuinely requires it.

For delete/mutation endpoints specifically: before reading implementation files, identify cascades (FK constraints, dependent services, cache entries, audit log hooks) — this scopes the read list before any files are opened. Verify DB-level `ON DELETE CASCADE` against the entity/migration file directly when it matters.

## Tool output handling (the highest-leverage habit after model-stacking)

Raw tool outputs (file contents, search results, query results) stay in context for the rest of the session and cost input tokens on every subsequent turn. After any tool call that returns substantial content:

- Summarize findings into the minimum needed to proceed: affected files + line ranges, relevant method signatures, cascade/security implications. Discard the raw output from your working notes.
- Never quote a full file, large JSON blob, or entire directory outputs into your response when a 3–5 line summary covers it.
- For the research → Alita handoff specifically: produce a structured compact note (see `Pythia.agent.md`), not a continuation of raw tool output.

This does not apply to: code you are actively editing (keep it exact), error messages (keep complete), security/customer-data warnings (keep complete).

## Output discipline (output tokens cost ~5x input)

- Don't restate the request before answering. Don't narrate what you're about to do — do it.
- After generating code: flag only what needs flagging (skipped ladder rung, cascade/auth implication, assumption made). No walkthrough of self-explanatory code.
- One example over several making the same point. No closing summary that repeats the diff.
- This never trims: safety-relevant flags, "never cut" items above, or an explanation explicitly asked for.

## Terse prose, when prose is needed

Cut: "in order to"→"to", "make sure to"→"ensure", "the reason is because"→"because", connective filler ("however", "furthermore"), pleasantries, hedging. Fragments are fine.

Never touch: code blocks, commands, file paths, error/log output, commit/PR text — exact, character for character. Don't drop conjunctions or sequence words near destructive operations — "migrate table, drop column, backup first" is ambiguous on order; that ambiguity is worse than a longer sentence.

Write in full, uncompressed: security/customer-data warnings, confirming a destructive or irreversible action (drops, deletes, force-pushes, migrations), anything explicitly asked to be explained in depth, any sign of confusion. When unsure, use the longer form.

## In practice

- Don't write a null-check cascade where `@Valid` + DTO constraints already enforce it.
- Don't add a new exception class if one in `exception` already fits.
- Don't write a per-controller response wrapper — use the existing envelope or `@RestControllerAdvice`.
- Don't add a repository method that duplicates one a derived-query rename away.
- Don't introduce a new bean/interface for something a `@Component` two files over already does.

## Specialized agents

Custom agents here have their own hardened instructions; this file layers on top. Each is pinned to the model suited to its job:

- **Investigation only, no edits** → `@pythia` (Pythia). Claude Haiku 4.5 — reading is token-heavy, doesn't need top-tier reasoning. Resolves against codebase symbols and search first.
- **Production code** → `@alita` (Alita). Claude Sonnet 4.6.
- **Security & vulnerability remediation** → `@aegis` (Aegis). Claude Sonnet 4.6.
- **Code review & quality gates** → `@themis` (Themis). Claude Haiku 4.5.
- **Tests & verification** → `@veronica` (Veronica). Claude Sonnet 4.6.
- Chain: `@pythia` → handoff → `@alita` → handoff → `@themis` / `@veronica`. Start at `@pythia` if investigation is needed first; go straight to `@alita` if the change is already clear.
- Anything else (planning, ambiguous asks) → default agent.

See `agents_java/*.agent.md` for each agent's full instructions and handoff note formats.

## Reviewing a diff — `/review`

Flags over-engineering and correctness in one pass. See `.github/prompts/review.prompt.md`.

## Commit messages — `/commit-message`

Terse Conventional Commits. See `.github/prompts/commit-message.prompt.md`.
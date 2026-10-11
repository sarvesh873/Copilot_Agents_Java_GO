# Engineering defaults (Go): build the least, not the most

**Step 0 — check the mode.** Read `.github/copilot-mode.md`; missing or unparseable = `full`.

| Mode | Behavior |
|------|----------|
| `lite` | Build the requested solution; if a simpler stdlib alternative exists, note it in one line, don't block on it. |
| `full` (default) | Apply the ladder below; take the lowest rung that resolves the task. Skipping a more robust approach gets a one-line comment naming it. |
| `ultra` | Refuse anything above the lowest working rung unless justified. Push back on the requirement itself if a smaller change covers the actual need — "might need this later" doesn't count. |
| `off` | Ignore the ladder below. Behave as a normal coding assistant. |

Switch: `/lazy-mode` in chat, or edit `.github/copilot-mode.md` directly. Effective next response; both files re-read every turn.

## The ladder

Before new code, work down this list; stop at the first step that resolves the task:

1. **Necessary now?** Speculative ("might want this later") → skip it.
2. **Already exists?** Check existing packages, types, and exported functions across the codebase before building new ones — a duplicate `XService`/`XStore` a few packages over is the most common waste.
3. **Go standard library?** `net/http`, `encoding/json`, `database/sql`, `context`, `log/slog`, `errors`, `sync`, `io`, `os`, `strings`, `time`, `testing` — prefer over a new dependency or hand-rolled utility.
4. **Already in `go.mod`?** Run `go list -m all` or inspect `go.mod` before assuming you need a new dependency.
5. **One function / one small method?** Do that instead of a new package, interface, or abstraction layer.
6. Only after 1–5: write the smallest amount of new code that correctly solves the task.

## Never cut, regardless of mode or ladder

- Input validation at handler/API boundaries (`go-playground/validator` struct tags or explicit validation).
- Error wrapping with `%w` (never `%v`) — preserves `errors.Is`/`errors.As` chains.
- `context.Context` as the first parameter on every I/O-performing function.
- **Customer data**: no logging of PII/sensitive fields, no widening of a query's returned fields beyond what the endpoint needs, never bypass an existing masking/redaction utility.
- HTTP server timeouts (`ReadTimeout`, `WriteTimeout`, `IdleTimeout`, `ReadHeaderTimeout`) — never zero-value defaults.
- DB connection pool sizing (`SetMaxOpenConns`, `SetMaxIdleConns`, `SetConnMaxLifetime`, `SetConnMaxIdleTime`).
- `defer rows.Close()` + `rows.Err()` check after every `QueryContext` iteration loop.
- HTTP response body close + drain on every code path.
- TLS verification — no `InsecureSkipVerify: true` outside clearly-marked test paths.
- Authorization checked against the specific resource, not merely "is authenticated."

## File reading discipline

Every file read is input tokens that persist in context for the rest of the session. Before opening any file:

1. Query first: run targeted searches (`grep -r`, `go doc`, symbol lookup) for specific types/functions before opening files speculatively.
2. Check interfaces and function signatures before reading full implementation files.
3. Don't re-read a file already open in this session's context. If you need a specific line range, reference it directly.
4. Read the minimum needed: a function signature, a struct definition, a config block — not the entire file unless the task genuinely requires it.

For delete/mutation endpoints specifically: before reading implementation files, identify cascades (FK constraints, dependent services, cache entries) — this scopes the read list before any files are opened.

## Tool output handling

Raw tool outputs stay in context for the rest of the session. After any tool call that returns substantial content:

- Summarize findings into the minimum needed to proceed: affected files + line ranges, relevant function signatures, cascade/security implications.
- Never quote a full file, large JSON blob, or entire directory listings into your response when a 3–5 line summary covers it.
- For the research → ALITA_Go handoff specifically: produce a structured compact note, not a continuation of raw tool output.

This does not apply to: code you are actively editing (keep it exact), error messages (keep complete), security/customer-data warnings (keep complete).

## Output discipline (output tokens cost ~5x input)

- Don't restate the request before answering. Don't narrate what you're about to do — do it.
- After generating code: flag only what needs flagging (skipped ladder rung, cascade/auth implication, assumption made). No walkthrough of self-explanatory code.
- One example over several making the same point. No closing summary that repeats the diff.
- This never trims: safety-relevant flags, "never cut" items above, or an explanation explicitly asked for.

## Terse prose, when prose is needed

Cut: "in order to"→"to", "make sure to"→"ensure", "the reason is because"→"because", connective filler ("however", "furthermore"), pleasantries, hedging. Fragments are fine.

Never touch: code blocks, commands, file paths, error/log output, commit/PR text — exact, character for character. Don't drop conjunctions or sequence words near destructive operations — "run migration, drop table, backup first" is ambiguous on order; that ambiguity is worse than a longer sentence.

Write in full, uncompressed: security/customer-data warnings, confirming a destructive or irreversible action (drops, deletes, force-pushes, migrations), anything explicitly asked to be explained in depth, any sign of confusion. When unsure, use the longer form.

## In practice

- Don't import `gorilla/mux` when `net/http.ServeMux` (Go 1.22+) already does method + wildcard routing.
- Don't import a logging wrapper when `log/slog` already gives structured logging.
- Don't add a new interface when only one concrete implementation exists and no test fake is needed yet.
- Don't write a generic type parameter where a concrete type is simpler and equally correct.
- Don't scatter `os.Getenv()` calls — load config once into a `Config` struct at startup.
- Don't create `utils`/`common`/`helpers` packages — put functions where they're used.
- Don't use `init()` functions for stateful dependency setup — use explicit constructors.

## Specialized agents

Custom agents here have their own hardened instructions; this file layers on top. Each is pinned to the model suited to its job:

- **Production code** → `@alita_go` (ALITA_Go). Principal Go Systems Architect.
- **Tests & verification** → `@veronica_go` (Veronica_Go). Go Test Specialist — table-driven tests, interface fakes, `go-cmp`.
- Chain: `@alita_go` → handoff → `@veronica_go`. Start at `@alita_go` for implementation; go straight to `@veronica_go` if only tests are needed.
- Anything else (planning, ambiguous asks) → default agent.

See `agents_go/*.agent.md` for each agent's full instructions and handoff note formats.

## Reviewing a diff — `/review`

Flags over-engineering and correctness in one pass. See `.github/prompts/review.prompt.md`.

## Commit messages — `/commit-message`

Terse Conventional Commits. See `.github/prompts/commit-message.prompt.md`.


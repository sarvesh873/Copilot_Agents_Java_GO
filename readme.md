# 🤖 AI Agent Workflow Guide (Java & Go)

Welcome to the Copilot AI Prompts repository. This repository organizes specialized GitHub Copilot instructions modularized by language and framework ecosystem:

* **`agents_java/`**: Specialized instructions for Java and Spring Boot 3+ development:
  * **Alita**: Principal Spring Boot Architect (Feature implementation & design patterns)
  * **Veronica**: Senior SDET & JUnit Specialist (High-coverage JUnit 5 & AssertJ suites)
  * **Themis**: Strict Code Reviewer & Gatekeeper (SOLID, OWASP security, proxy & JPA correctness)
  * **Aegis**: Security Remediation Engineer (Automated Fortify vulnerability fixing & Maven BOM resolution)
  * **Pythia**: Codebase Investigator (Read-only dependency tracing & architectural research)
* **`agents_go/`**: Instructions for Go / Golang systems engineering:
  * **ALITA_Go**: Principal Go Systems Architect (Idiomatic Go, stdlib-first, concurrency safety)
  * **Veronica_Go**: Senior Go Test Specialist (Table-driven tests, interface fakes, `go-cmp`, `httptest`)

---

## 📁 Repository Structure

```text
.
├── .github/
│   ├── prompts/
│   │   ├── commit-message.prompt.md   # Terse Conventional Commit generator (/commit-message)
│   │   └── review.prompt.md           # Unified PR/diff review & simplicity gate (/review)
│   └── copilot-mode.md                # Engineering ladder strictness mode switch
├── agents_java/
│   ├── Aegis.agent.md                 # Security Remediation & Fortify Auto-Fix
│   ├── Alita.agent.md                 # Principal Spring Boot 3+ Architect
│   ├── Pythia.agent.md                # Codebase Investigation & Research (Read-Only)
│   ├── Themis.agent.md                # Strict Spring Boot Code Reviewer & Gatekeeper
│   └── Veronica.agent.md              # Senior SDET / JUnit 5 & Mockito Specialist
├── agents_go/
│   ├── ALITA_Go.agent.md              # Principal Go Systems Architect
│   └── Veronica_Go.agent.md           # Senior Go Test Specialist
├── scripts/
│   └── fod_vulnerability_extractor.py # FoD SAST/SCA Vulnerability Extraction CLI
├── copilot-instructions.md            # Global Engineering Defaults & Agent Chain (Java)
├── copilot-instructions-go.md         # Global Engineering Defaults & Agent Chain (Go)
└── readme.md
```

---

## ☕ Java & Spring Boot Agents (`agents_java/`)

### 🏗️ 1. Alita (Principal Spring Boot Architect)
**File:** `agents_java/Alita.agent.md`

Your Principal Engineer pair-programmer. Enforces SOLID principles, Library-First mandates, DRY/KISS architecture, and decomposes complex classes into clean, production-grade Spring Boot 3.x components.

* **When to use it:**
  * Building a new feature or service from scratch.
  * Refactoring legacy classes or splitting "God Classes".
  * Implementing clean controller -> service -> repository domain boundaries.
* **How to invoke:**
  > `@workspace #file:agents_java/Alita.agent.md I need to implement a new StripePaymentService. It should take a PaymentRequest record and process it securely. Please build the service.`

---

### 🧪 2. Veronica (Senior SDET / JUnit Specialist)
**File:** `agents_java/Veronica.agent.md`

Your dedicated Software Development Engineer in Test. Generates defensible, edge-case resilient unit test suites using JUnit 5, Mockito, and AssertJ. Enforces strict mock boundaries and anti-overmocking plausibility gates.

* **When to use it:**
  * Generating test coverage immediately after writing a new service or controller.
  * Building test harnesses for legacy code before refactoring.
  * Testing edge cases, boundary values, and negative exception branches.
* **How to invoke:**
  > `Review the open file. #file:agents_java/Veronica.agent.md Please generate a comprehensive JUnit 5 test suite for this service. Ensure you test negative inputs and null values.`

---

### 🕵️‍♂️ 3. Themis (Strict Code Reviewer & Gatekeeper)
**File:** `agents_java/Themis.agent.md`

Your strict quality gatekeeper. Reviews diffs and classes against SOLID standards, OWASP-aligned security vulnerabilities, N+1 JPA query performance, Spring proxy/self-invocation pitfalls, and configuration leaks.

* **When to use it:**
  * Prior to opening a Pull Request.
  * Performing automated sanity reviews on a teammate's PR.
  * Verifying architectural integrity after major refactorings.
* **How to invoke:**
  > `@workspace #file:agents_java/Themis.agent.md Please perform a strict code review on my latest changes. Flag any field injections, unhandled exceptions, or proxy bypasses.`

---

### 🛡️ 4. Aegis (Fortify Security Remediation Engineer)
**File:** `agents_java/Aegis.agent.md`

Specialized in automated Fortify on Demand (FoD) SAST/SCA security discovery and remediation. Diagnoses vulnerable Maven dependencies and applies patches using a strict top-down BOM property hierarchy.

* **When to use it:**
  * Triaging and remediating Fortify/FoD vulnerability reports (`pom_updates_for_copilot.json`).
  * Resolving transitive dependency CVEs safely via Maven BOM properties.
  * Remediating code-level findings (Path Traversal, SQLi, CRLF Log Forging).
* **Companion CLI Script:** `scripts/fod_vulnerability_extractor.py` (automates FoD REST API token retrieval, vulnerability filtering, and JSON dataset generation).
* **How to invoke:**
  > `@workspace #file:agents_java/Aegis.agent.md Please analyze the High severity dependencies from pom_updates_for_copilot.json and update pom.xml following the strict dependency hierarchy.`

---

### 🔍 5. Pythia (Codebase Investigator & Research)
**File:** `agents_java/Pythia.agent.md`

A strictly read-only investigator agent. Explores codebases, traces dependency trees, verifies call paths, and checks authentication/cascade impacts before preparing a structured handoff note for Alita.

* **When to use it:**
  * Investigating "where is X implemented" or "what calls Y".
  * Scoping cascades and security gates prior to destructive or mutation changes.
  * Performing preliminary architecture investigations without risk of modifying files.
* **How to invoke:**
  > `@workspace #file:agents_java/Pythia.agent.md Where is the order processing workflow initiated, and what services handle payments downstream?`

---

## 🐹 Go / Golang Agents (`agents_go/`)

### 🏗️ 1. ALITA_Go (Principal Go Systems Architect)
**File:** `agents_go/ALITA_Go.agent.md`

Principal Systems Architect for Go backend systems. Enforces idiomatic Go, standard-library-first architecture, composition over inheritance, strict concurrency safety (goroutine leak prevention, nil-interface traps, channel deadlock guards), and table-driven testing.

* **When to use it:**
  * Designing and developing production-grade Go services, REST APIs, or CLI tools.
  * Refactoring Go packages into clean layers (`Handler` -> `Service` -> `Store`) with consumer-defined interfaces.
  * Conducting pre-flight architectural audits for concurrency, memory allocation, and error-wrapping compliance (`%w`).
* **How to invoke:**
  > `@workspace #file:agents_go/ALITA_Go.agent.md I need to implement a user authentication service in Go with standard library HTTP routing and context cancellation. Please design and build the service.`

---

### 🧪 2. Veronica_Go (Senior Go Test Specialist)
**File:** `agents_go/Veronica_Go.agent.md`

Dedicated Go test engineer. Generates defensible, table-driven test suites using the standard `testing` package, `google/go-cmp` for structural comparisons, and hand-written interface fakes. Enforces strict anti-echo-test plausibility gates, `t.Parallel()` discipline, and `go test -race` readiness.

* **When to use it:**
  * Generating comprehensive test coverage after writing a new Go service or handler.
  * Writing table-driven unit tests with proper subtesting (`t.Run`), `t.Cleanup`, and `t.Helper`.
  * Testing error paths (`errors.Is`/`errors.As`), concurrency edge cases, and HTTP handlers via `httptest`.
  * Building fuzz tests (`FuzzXxx`) and benchmarks (`BenchmarkXxx`) for input-parsing or performance-critical code.
* **Key features:**
  * 14 zero-tolerance hard rules for Go test quality.
  * 20-point pre-write checklist verified before every test.
  * Dedicated HTTP handler testing template with `httptest.NewRequest` + `httptest.NewRecorder`.
  * `testify` interop guidance when the project already uses it.
  * Build tag discipline (`//go:build integration`) and `testing.Short()` support.
* **How to invoke:**
  > `@workspace #file:agents_go/Veronica_Go.agent.md Please generate a comprehensive table-driven test suite for this Go service. Cover error paths, nil inputs, and context cancellation.`

---

## 🚀 Recommended Agent Chains & Workflows

### ☕ Spring Boot Team Chain
1. **Investigate (`@pythia`):** Scope required changes, trace dependencies, check cascades, and produce a structured handoff note.
2. **Build (`@alita`):** Receive handoff note and implement clean, library-first, SOLID Spring Boot services and controllers.
3. **Test (`@veronica`):** Generate complete, parameterized JUnit 5 + Mockito test suites covering all positive and negative branches.
4. **Review (`@themis`):** Run an automated pre-PR review gate to verify zero architectural, concurrency, or security defects.
5. **Security Audit (`@aegis`):** Automate Fortify vulnerability scanning and resolve vulnerable transitive dependencies via BOM properties.

### 🐹 Go / Golang Team Chain
1. **Design & Stdlib Scan (`@alita_go`):** Invoke `#file:agents_go/ALITA_Go.agent.md` to plan package boundaries, verify standard library coverage (`net/http`, `log/slog`, `database/sql`), and establish consumer interfaces.
2. **Implement (`@alita_go`):** Write idiomatic Go with explicit error checking (`%w`), context propagation, and fail-fast startup configs.
3. **Test (`@veronica_go`):** Invoke `#file:agents_go/Veronica_Go.agent.md` to generate table-driven tests with interface fakes, `go-cmp` assertions, and `go test -race` readiness.
4. **Audit:** Use ALITA_Go's Ironclad Pre-Flight Self-Audit to verify zero goroutine leaks, nil-interface safety, and idiomatic consistency.
5. **Push:** Commit idiomatic, production-ready Go code with matching `*_test.go` files.

### 📋 Copilot Instructions & Prompt Files
| File | Scope & Trigger | Description |
|------|-----------------|-------------|
| `copilot-instructions.md` | Persistent (Java / Spring Boot) | Global engineering ladder, architectural defaults, never-cut rules |
| `copilot-instructions-go.md` | Persistent (Go / Golang) | Stdlib-first ladder, Go-specific never-cut rules, agent chains |
| `.github/copilot-mode.md` | Workspace Config (`full`, `lite`, `ultra`, `off`) | Controls strictness of the engineering ladder |
| `.github/prompts/review.prompt.md` | Slash Command `/review` | Fast, multi-language diff review checking over-engineering, security & correctness |
| `.github/prompts/commit-message.prompt.md` | Slash Command `/commit-message` or ✨ Sparkle | High-density Conventional Commit generator from git diff |

---

## ⚡ Reusable Slash Commands (`.github/prompts/`)

### 🔎 `/review` (Unified Code Review)
**File:** `.github/prompts/review.prompt.md`

Performs an intent-first, grounded code review on your git diff, staged changes, or pull request. Evaluates code against the Engineering Ladder (preventing over-engineering), OWASP security vulnerabilities, language-specific pitfalls (Spring proxy self-invocation, Go goroutine leaks/nil interfaces), and test coverage.

* **How to trigger:**
  > `/review` in Copilot Chat (or select code / diff and ask `/review`)

---

### ✍️ `/commit-message` (Conventional Commit Generator)
**File:** `.github/prompts/commit-message.prompt.md`

Generates terse, standardized Git commit messages strictly adhering to the **Conventional Commits 1.0.0** specification. Automatically picks the right type (`feat`, `fix`, `refactor`, `perf`, `security`), enforces imperative mood, keeps the subject line under 72 characters, and explains the rationale (WHY) in the body.

* **How to trigger:**
  > `/commit-message` in Copilot Chat, or click the ✨ sparkle icon in VS Code Source Control.

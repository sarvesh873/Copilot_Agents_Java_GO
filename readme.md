# 🤖 AI Agent Workflow Guide (Java & Go)

Welcome to the Copilot AI Prompts repository. This repository organizes specialized GitHub Copilot instructions modularized by language and framework ecosystem:

* **`agents_java/`**: Instructions for Java and Spring Boot 3+ (Architect, JUnit Test Specialist, Code Reviewer).
* **`agents_go/`**: Instructions for Go / Golang systems engineering (Principal Go Systems Architect, with additional Go agents planned).

By using these instructions, you transform standard GitHub Copilot from a "generic code autocomplete" tool into a Principal Architect, an SDET, and a strict Code Reviewer.

---

## 📁 Repository Structure

```text
.
├── agents_java/
│   ├── spring-architect.md        # Principal Spring Boot 3+ Architect
│   ├── junit-instructions.md      # Expert JUnit 5 & AssertJ Test Engineer
│   └── spring-code-reviewer.md    # Strict Spring Boot Code Reviewer
├── agents_go/
│   └── ALITA_Go.md                # Principal Go Systems Architect
└── readme.md
```

---

## ☕ Java & Spring Boot Agents (`agents_java/`)

### 🛠️ 1. The Spring Architect (For Writing Code)
**File:** `agents_java/spring-architect.md`

This is your Principal Engineer pair-programmer. It focuses heavily on design patterns, DRY/KISS principles, and decomposing large classes.

* **When to use it:**
  * Starting a new feature from scratch (e.g., building a new Service or Controller).
  * Refactoring a messy, existing class.
  * When you need to split a "God Class" into smaller, SRP-compliant components.
* **How to invoke:** Open Copilot Chat and tag the file.
  > `@workspace #file:agents_java/spring-architect.md I need to implement a new StripePaymentService. It should take a PaymentRequest record and process it securely. Please build the service.`

---

### 🧪 2. The JUnit Specialist (For Writing Tests)
**File:** `agents_java/junit-instructions.md`

This is your SDET (Software Development Engineer in Test). It does not write lazy tests. It forces high coverage, edge-case handling, and strict mocking boundaries.

* **When to use it:**
  * Immediately after writing a new Service or Controller.
  * When modifying legacy code and you need to build a test harness first.
  * When trying to increase your Jacoco test coverage.
* **How to invoke:** Open the class you want to test, open Copilot Chat, and tag the file.
  > `Review the open file. #file:agents_java/junit-instructions.md Please generate a comprehensive JUnit 5 test suite for this service. Ensure you test negative inputs and null values.`

---

### 🕵️‍♂️ 3. The Spring Code Reviewer (For PRs & Sanity Checks)
**File:** `agents_java/spring-code-reviewer.md`

This is your strict gatekeeper. It acts like a Senior Reviewer looking for architectural flaws, security risks, and performance bottlenecks (like N+1 queries) before you merge your code.

* **When to use it:**
  * Right before opening a Pull Request.
  * When reviewing a teammate's PR and you want an automated "second pair of eyes."
  * When you've finished a complex refactoring and want to ensure you didn't break SOLID rules.
* **How to invoke:** Select the code you just wrote (or open the git diff), open Copilot Chat, and tag the file.
  > `@workspace #file:agents_java/spring-code-reviewer.md Please perform a strict code review on my latest changes. Flag any field injections or missing exception handling.`

---

## 🐹 Go / Golang Agents (`agents_go/`)

### 🏗️ 1. ALITA_Go (Principal Go Systems Architect)
**File:** `agents_go/ALITA_Go.md`

This is your Principal Software Engineer and Systems Architect for Go backend systems. It enforces idiomatic Go, standard-library-first architecture, composition over inheritance, strict concurrency safety (goroutine leak prevention, nil-interface traps, channel deadlock guards), and table-driven testing.

* **When to use it:**
  * Designing and developing production-grade Go services, REST APIs, or CLI tools.
  * Refactoring Go packages into clean layers (`Handler` -> `Service` -> `Store`) with consumer-defined interfaces.
  * Conducting pre-flight architectural audits for concurrency, memory allocation, and error-wrapping compliance (`%w`).
* **How to invoke:** Open Copilot Chat and tag the file.
  > `@workspace #file:agents_go/ALITA_Go.md I need to implement a user authentication service in Go with standard library HTTP routing and context cancellation. Please design and build the service.`

*(Note: Additional specialized Go agents, such as test specialists and automated code reviewers, are planned for this directory).*

---

## 🚀 Recommended Workflows

### ☕ Spring Boot Workflow

To get the most out of these tools, use them in sequence during your daily development:

1. **Build:** Use `#file:agents_java/spring-architect.md` to generate clean, SOLID-compliant service and controller skeletons.
2. **Refine:** Write the domain business logic and configure dependencies via constructor injection.
3. **Test:** Open the target class and invoke `#file:agents_java/junit-instructions.md` to produce comprehensive, edge-case JUnit 5 and AssertJ suites.
4. **Review:** Highlight changes and use `#file:agents_java/spring-code-reviewer.md` to run an iron-clad pre-PR review (checking for N+1 queries, OWASP issues, and config leaks).
5. **Push:** Merge clean, tested, and validated code.

### 🐹 Go / Golang Workflow

1. **Design & Stdlib Scan:** Invoke `#file:agents_go/ALITA_Go.md` to plan package boundaries, verify standard library coverage (`net/http`, `log/slog`, `database/sql`), and establish consumer interfaces.
2. **Implement:** Write idiomatic Go with explicit error checking (`%w`), context propagation, and fail-fast startup configs.
3. **Audit & Test:** Use ALITA_Go's Ironclad Pre-Flight Self-Audit to verify zero goroutine leaks, nil-interface safety, and generate table-driven unit tests.
4. **Push:** Commit idiomatic, production-ready Go code.


# 🤖 Spring Boot AI Agent Workflow Guide

Welcome to the Spring Boot AI Prompts repository. This folder contains a set of highly specialized GitHub Copilot instructions designed to enforce Clean Code, SOLID principles, and strict Java/Spring Boot architectural standards across our team.

By using these instructions, you transform standard GitHub Copilot from a "generic code autocomplete" tool into a Principal Architect, an SDET, and a strict Code Reviewer.

---


## 🛠️ 1. The Spring Architect (For Writing Code)
**File:** `.github/instructions/spring-architect.md`

This is your Principal Engineer pair-programmer. It focuses heavily on design patterns, DRY/KISS principles, and decomposing large classes.

* **When to use it:** * Starting a new feature from scratch (e.g., building a new Service or Controller).
  * Refactoring a messy, existing class.
  * When you need to split a "God Class" into smaller, SRP-compliant components.
* **How to invoke:** Open Copilot Chat and tag the file.
  > `@workspace #file:spring-architect.md I need to implement a new StripePaymentService. It should take a PaymentRequest record and process it securely. Please build the service.`

---

## 🧪 2. The JUnit Specialist (For Writing Tests)
**File:** `.github/instructions/junit-specialist.md`

This is your SDET (Software Development Engineer in Test). It does not write lazy tests. It forces high coverage, edge-case handling, and strict mocking boundaries.

* **When to use it:**
  * Immediately after writing a new Service or Controller.
  * When modifying legacy code and you need to build a test harness first.
  * When trying to increase your Jacoco test coverage.
* **How to invoke:** Open the class you want to test, open Copilot Chat, and tag the file.
  > `Review the open file. #file:junit-specialist.md Please generate a comprehensive JUnit 5 test suite for this service. Ensure you test negative inputs and null values.`

---

## 🕵️‍♂️ 3. The Code Reviewer (For PRs & Sanity Checks)
**File:** `.github/instructions/spring-code-reviewer.md`

This is your strict gatekeeper. It acts like a Senior Reviewer looking for architectural flaws, security risks, and performance bottlenecks (like N+1 queries) before you merge your code.

* **When to use it:**
  * Right before opening a Pull Request.
  * When reviewing a teammate's PR and you want an automated "second pair of eyes."
  * When you've finished a complex refactoring and want to ensure you didn't break SOLID rules.
* **How to invoke:** Select the code you just wrote (or open the git diff), open Copilot Chat, and tag the file.
  > `@workspace #file:spring-code-reviewer.md Please perform a strict code review on my latest changes. Flag any field injections or missing exception handling.`

---

## 🚀 The "Daily Grind" Example Workflow

To get the most out of these tools, use them in sequence during your daily development:

1. **Build:** You get a Jira ticket to add a User Notification feature. You use `#file:spring-architect.md` to generate the `NotificationService.java` skeleton.
2. **Refine:** You write the specific business logic.
3. **Test:** You open `NotificationService.java` and use `#file:junit-specialist.md` to generate the test cases. You run the tests and fix any bugs.
4. **Review:** Before committing, you highlight your new code and use `#file:spring-code-reviewer.md` to do a final sanity check against our Spring Boot standards.
5. **Push:** You push clean, tested, architecturally sound code.

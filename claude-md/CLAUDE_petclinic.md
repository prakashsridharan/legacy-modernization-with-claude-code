# CLAUDE.md — Spring PetClinic Modernization Demo

> Demonstration of Claude Code for legacy modernization, using the Spring PetClinic
> sample application. Treats PetClinic as if it were a real enterprise system facing
> modernization, to show the workflow on a small, reproducible codebase.

---

## 1. Project Context

- **System:** Spring PetClinic
- **Domain:** Veterinary clinic — owner/pet management, visits, vet directory
- **Repository:** github.com/spring-projects/spring-petclinic
- **Treating as:** A "legacy" monolith for demonstration purposes
- **Real-world analogue:** Customer master + service appointments (banking, telecom, SaaS)

> **Note:** PetClinic is actually a well-structured Spring sample. We are demonstrating the *workflow* of AI-assisted modernization on a recognizable codebase. The same prompts and patterns work identically on a 500,000-line enterprise monolith — just with longer outputs and more sessions.

---

## 2. Stack & Environment

### Languages & Frameworks
- **Backend:** Java 17, Spring Boot 3.x
- **Persistence:** Spring Data JPA (Hibernate under the hood)
- **Database:** H2 (in-memory by default), MySQL and PostgreSQL profiles available
- **Build:** Maven (`./mvnw`)
- **Frontend:** Thymeleaf templates (out of scope for this demo)

### Conventions
- Spring Data JPA repositories — no raw JDBC, no JdbcTemplate
- Bean Validation via `@Valid` on controller methods; constraints on entity fields
- Constructor injection on services and controllers
- Controllers return view names (server-side MVC), not JSON

### Runtime
- Java 17 required; do not introduce Java 21+ features
- Tests use JUnit 5 + Spring Boot Test + MockMvc

---

## 3. Architecture Overview

- **Pattern:** Layered monolith — controller → service → repository → entity
- **Bounded contexts (discovered from the package layout):**
  - `owner` — Owner master, pets-of-owner aggregate, visit history listing
  - `vet` — Vet directory and their specialties
  - `system` — Welcome page, crash controller (demo scaffolding)
- **Key entities:** `Owner`, `Pet`, `PetType`, `Visit`, `Vet`, `Specialty`
- **Coupling notes:**
  - `Owner` aggregates `Pet`; `Pet` aggregates `Visit` — these travel together
  - `Vet` and `Specialty` are isolated from `owner` — natural extraction candidate
  - No cross-aggregate writes outside the owner-pet-visit graph

---

## 4. Modernization Demo Goals

This CLAUDE.md exists to demonstrate four use cases on a small, recognizable codebase:

1. **Module dependency mapping** — surface real boundaries from code
2. **Business rule extraction** — turn validation logic into a plain-language spec
3. **Pre-refactor characterization tests** — lock in behaviour before changing it
4. **Strangler pattern proposal** — extract the `vet` package as a standalone service

Each goal maps to a prompt in `DEMO.md`.

---

## 5. Workflow Rules for Claude Code

### When analyzing this codebase
- Read entire packages before suggesting changes, not just one file at a time
- For each finding, cite source `File.java:LINE` so the reader can verify
- Distinguish observed behaviour from inferred intent

### When extracting business rules
- Look in: entity field constraints, controller validation methods, service-layer guards
- Each rule needs: plain-language description, source location, confidence level
- Flag any rule where you had to guess at intent

### When generating tests
- Use JUnit 5 + Spring's `MockMvc`, matching the style in `src/test/java/org/springframework/samples/petclinic/owner/`
- Tests should fail if the contract changes, not just if the implementation changes
- Cover: happy path, each validation constraint, edge cases, exception paths

### When proposing extractions
- Identify minimum viable extraction — what is the smallest seam to cut?
- List every coupling point that would need to break
- Propose a phased plan that keeps the monolith deployable at every step

### What Claude must NOT do
- Do not actually modify the upstream Spring PetClinic repo (read-only analysis)
- Do not propose changes to the Thymeleaf view layer (out of scope)
- Do not suggest replacing JPA with another ORM — that is not the modernization goal here

---

## 6. People & Ownership

- **Demo curator:** Prakash Sridharan
- **Source repo:** Spring team (read-only — we do not submit PRs here)
- **Audience:** LinkedIn followers, engineering teams evaluating Claude Code for their own modernization

---

*Last reviewed: <DATE>. This is a demo file — keep it short and stable. The point is to show the workflow, not to grow the file.*

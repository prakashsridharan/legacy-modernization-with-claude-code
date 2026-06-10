# DEMO.md — Running the Claude Code Modernization Demo on Spring PetClinic

> A reproducible 30-minute walkthrough showing Claude Code for legacy modernization.
> Output: 5 concrete, verifiable artefacts that prove the workflow on a small, recognizable codebase.

---

## What You'll Produce

By the end of this demo you will have:

1. A **dependency map** of the `owner` module
2. A **business rules table** extracted from the controller layer
3. A **characterization test class** that locks in current behaviour
4. A **strangler extraction plan** for the `vet` module
5. A **session summary** showing how CLAUDE.md grows organically

Each output is a concrete, verifiable artefact.

---

## Prerequisites

- Claude Code installed and authenticated
- Git, Java 17+, Maven (or use the `./mvnw` wrapper that ships with PetClinic)
- About 30 minutes of focused time

---

## Setup (5 minutes)

```bash
# Clone PetClinic
git clone https://github.com/spring-projects/spring-petclinic.git
cd spring-petclinic

# Drop the demo CLAUDE.md into the repo root
# (download CLAUDE_petclinic.md from your kit, rename it to CLAUDE.md)
cp /path/to/CLAUDE_petclinic.md ./CLAUDE.md

# Verify it builds (optional but recommended)
./mvnw -q -DskipTests package

# Launch Claude Code from the repo root
claude
```

**Smoke test:**
```
You: "What is this codebase and what is in CLAUDE.md?"
```

Expected: Claude should mention Spring PetClinic, list the bounded contexts from section 3 of CLAUDE.md, and reference the four demo goals from section 4. If it doesn't, the file isn't being picked up — check you are in the repo root and the file is named exactly `CLAUDE.md`.

---

## Step 1 — Module Dependency Map *(5 minutes)*

### Prompt
```
Map the `owner` package. For each public class:
- its dependencies on other packages
- the entities and database tables it touches
- the controller endpoints that depend on it
- any coupling to packages outside `owner` that would block extracting it as a standalone service.

Cite source File.java:LINE for every claim.
```

### What good output looks like
You should see:
- A table or list with each class in `owner` and its responsibilities
- Mention of `Owner`, `Pet`, `Visit`, `PetType` entities
- Mention of the `owners`, `pets`, `visits` database tables (via `data.sql` / `schema.sql`)
- Identification that `owner` is *self-contained* — minimal external coupling

### What to capture
**Screenshot:** The full dependency map output, with at least one cited `File.java:LINE` reference visible.

### Pro tip
If the output is too long for one screenshot, ask:
```
Now compress this into a single summary table I can screenshot.
```

---

## Step 2 — Business Rule Extraction *(7 minutes)*

### Prompt
```
Extract every business rule embedded in OwnerController and PetController into a numbered table.

For each rule include:
- plain-language description
- source File.java:LINE
- validation constraints involved
- confidence level (High/Medium/Low)

Pay special attention to:
- Validation rules on Owner and Pet fields
- The pet-add flow
- Any date checks (e.g., birth date constraints)
- Any duplicate-pet-name checks for an owner
```

### What good output looks like
A table with rows like:

| Rule ID | Description | Source | Confidence |
|---|---|---|---|
| BR-01 | Owner first name and last name are mandatory | `Owner.java:45-48` | High |
| BR-02 | Owner address, city, telephone are mandatory | `Owner.java:50-58` | High |
| BR-03 | Pet birth date cannot be in the future | `Pet.java:LINE` | High |
| BR-04 | Pet name must be unique within an owner | `Owner.java:LINE` | High |

The exact rules and line numbers will vary based on PetClinic's current state.

### What to capture
**Screenshot:** The rules table.

### Pro tip — make it screenshot-perfect
After Claude produces the table, run:
```
Reformat as a Markdown table with these columns exactly: Rule ID | Description | Source | Confidence.
```

---

## Step 3 — Characterization Tests *(7 minutes)*

### Prompt
```
Before I refactor OwnerController.processCreationForm(), generate characterization tests
that lock in current behavior.

Cover:
- Valid owner creation (happy path)
- Validation failure for each missing required field
- The redirect behavior on success
- BindingResult handling
- Any side effects on the model

Use JUnit 5 and Spring's MockMvc, matching the style already in
src/test/java/org/springframework/samples/petclinic/owner/OwnerControllerTests.java.
```

### What good output looks like
A complete test class with 5-8 `@Test` methods covering happy path and validation failures, using `MockMvc.perform()` calls that match the existing test conventions.

### What to capture
**Screenshot:** The first 25-30 lines of the generated test class — enough to show the class declaration, imports, and 2-3 representative test methods.

### Optional: actually run the tests
```bash
# Save Claude's output to a new test file (don't overwrite existing tests)
# Then run:
./mvnw test -Dtest=OwnerControllerCharacterizationTests
```

If they pass, screenshot the green output.

### Pro tip
Don't worry if Claude misses an edge case or two — the point is to demonstrate the *workflow*, not produce a perfect test suite. If you want, append:
```
What edge cases did you miss? Add tests for any you can identify by re-reading the controller.
```
This shows the iterative refinement, which is also valuable.

---

## Step 4 — Strangler Extraction Proposal *(8 minutes)*

### Prompt
```
Propose extracting the `vet` package as a standalone microservice.

Produce:
1. The list of classes that would move to the new service
2. The public API the service would expose (HTTP endpoints, request/response shapes)
3. The coupling points with the existing monolith and how to break them
4. A phased extraction plan that keeps the monolith deployable at every step
5. The risks specific to this extraction
```

### What good output looks like
A structured proposal with five clearly labelled sections:

1. **Classes to move:** `Vet.java`, `Vets.java`, `Specialty.java`, `VetRepository.java`, `VetController.java`
2. **Public API:** Probably `GET /vets`, `GET /vets/{id}`, `GET /specialties`
3. **Coupling points:** Likely minimal — vet data is read-only from the rest of the system
4. **Phased plan:** Usually four phases — copy → dual-write → cutover → cleanup
5. **Risks:** Caching, view-layer (Thymeleaf) coupling, test-data fixtures

### What to capture
**Screenshot:** Sections 1, 2, and the first phase of the extraction plan.

### Pro tip
This output is naturally long. Ask Claude to:
```
Compress points 3, 4, and 5 into a single one-page summary I can screenshot.
```

---

## Step 5 — Session Summary *(3 minutes)*

### Prompt
```
Summarize what we learned in this session that should be persisted in CLAUDE.md.

Format as concrete additions to the relevant sections:
- New business rules to add to the Business Rules Registry
- New architectural insights for section 3
- Any gotchas worth remembering
```

### What good output looks like
A bulleted list of proposed additions, each tagged with which CLAUDE.md section it belongs in.

### What to capture
**Screenshot:** The summary output — this shows CLAUDE.md as a living artefact that grows as the team learns.

---

## Common Pitfalls to Avoid

| Pitfall | Fix |
|---|---|
| Claude doesn't reference CLAUDE.md | You launched from the wrong directory. Must be the repo root. |
| Output too long for one screenshot | Ask Claude to compress: *"Summarize the above into a screenshot-sized table"* |
| Tests Claude generates won't compile | For the demo, the *content* matters more than exact compile success. Run `./mvnw test` to validate. |
| Output looks generic | Re-prompt with more specificity from CLAUDE.md: *"Use the exact bounded contexts from section 3 of CLAUDE.md"* |
| You want to redo a step cleanly | Start a fresh Claude Code session (`/clear` or restart) so the context is clean |

---

*This demo is reproducible. Anyone with Claude Code, Java, and 30 minutes can run it on their own machine.*

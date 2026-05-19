# CLAUDE.md — Legacy Modernization Playbook

> ⚠️ **EXAMPLE CONTENT — DO NOT USE AS-IS** ⚠️
>
> Everything in this file is **illustrative and fabricated**. File names, line numbers, rule IDs, stack versions, percentages, dates, and people are all invented to show the *format and level of detail* a real CLAUDE.md should contain.
>
> All fabricated values are flagged with `[EXAMPLE]`.
>
> Before using this file on a real project:
> 1. Replace every `[EXAMPLE]` value with your real data
> 2. Delete the BR-001 through BR-004 sample rules entirely — they are not real rules
> 3. Strip this warning banner
> 4. Have your modernization lead and a domain owner review before committing to Git

---

> Persistent project memory for AI-assisted modernization.
> Checked into Git. Owned by the modernization team. Maintained like production code.

This file is read by Claude Code at the start of every session. Keep it concise, current, and high-signal. For each line, ask: *"Would removing this cause Claude to make mistakes?"* If not, cut it.

---

## 1. Project Context

**System name:** `CoreLedger` `[EXAMPLE]`
**Domain:** Retail banking core — account master, transactions, billing `[EXAMPLE]`
**Criticality:** Tier-1 — supports ~70% of daily transaction volume `[EXAMPLE]`
**Repository:** `<git URL>` `[EXAMPLE — replace with your actual repo URL]`
**First commit:** 2006 `[EXAMPLE]` • **Team history:** original team disbanded 2014; current team is the second handover `[EXAMPLE]`
**Lines of code (approx):** 480,000 Java + 90,000 SQL + 35,000 shell `[EXAMPLE]`

---

## 2. Stack & Environment

> ⚠️ Everything below is a **realistic composite** of typical enterprise legacy systems, not from any real codebase.

### Languages & Frameworks `[EXAMPLE]`
- **Backend:** Java 8 (target: Java 21), Spring Boot 2.3.x → 3.x, Hibernate 4.x
- **Database:** Oracle 11g (target: PostgreSQL 16)
- **Messaging:** IBM MQ 8 (target: Kafka)
- **Build:** Maven 3.6, single multi-module POM, ~140 modules
- **Frontend:** JSP + Struts 1.x (out of scope for this modernization)

### Conventions (NEVER violate) `[EXAMPLE]`
- All monetary calculations use `BigDecimal` with `RoundingMode.HALF_UP`, scale = 4
- All timestamps stored as UTC; display layer handles timezone conversion
- Database column names are `SNAKE_CASE`; Java field names are `camelCase`
- Audit columns (`CREATED_BY`, `CREATED_TS`, `UPDATED_BY`, `UPDATED_TS`) on every table
- Service-layer methods are transactional by default; repository methods are not

### Runtime `[EXAMPLE]`
- WebSphere 8.5 (legacy) → Tomcat 10 / OpenLiberty (modernization target)
- JDK 1.8.0_281 in production; do not introduce code that requires a newer JDK without flagging

---

## 3. Architecture Overview

> ⚠️ Module names, file paths, and line numbers below are **all fabricated**.

- **Pattern:** Monolithic 3-tier with shared DB; 45 SOAP endpoints + 12 REST endpoints `[EXAMPLE]`
- **Bounded contexts (discovered, NOT designed):** `[EXAMPLE]`
  - `customer.*` — customer master, KYC, preferences
  - `account.*` — account opening, hierarchy, status
  - `txn.*` — transaction processing (hot path, 80% of load)
  - `billing.*` — fee calculation, invoicing, dunning
  - `compliance.*` — AML, sanctions screening, reporting
  - `batch.*` — overnight EOD jobs, reconciliation
- **High-coupling hotspots (modernize LAST):** `[EXAMPLE — these classes do not exist]`
  - `TransactionService.processBatch()` — 2,800-line method, called from 4 schedulers
  - `BillingEngine.calculate()` — embeds tax, fee, and rebate logic; ~600 if/else branches
- **Forbidden cross-module calls:** `compliance.*` must never depend on `billing.*` (regulatory separation) `[EXAMPLE]`

---

## 4. Business Rules Registry

> ⚠️ **DELETE THESE EXAMPLE RULES BEFORE USING THIS FILE ON A REAL PROJECT.**
> BR-001 through BR-004 below are **invented for illustration only**. They are not real rules from any system. Do not copy them into a production CLAUDE.md.

Critical rules extracted from legacy code. Every rule is the contract for the modernized version. **Never** modernize a module without first extracting and reviewing its rules with the business.

| Rule ID | Module | Description | Source | Status |
|---------|--------|-------------|--------|--------|
| BR-001 `[EXAMPLE]` | billing | Late fee = 2% of balance, min ₹500, max ₹5,000 | `BillingEngine.java:412-438` `[FABRICATED FILE]` | Reviewed |
| BR-002 `[EXAMPLE]` | billing | Grandfathered customers (acct < 2010) exempt from BR-001 | `BillingEngine.java:445-461` `[FABRICATED FILE]` | Reviewed |
| BR-003 `[EXAMPLE]` | account | Min balance ₹10,000 for "Premium"; ₹1,000 for "Standard" | `AccountValidator.java:88-104` `[FABRICATED FILE]` | Pending review |
| BR-004 `[EXAMPLE]` | txn | Cross-border txns above ₹50,000 trigger compliance hold | `TxnRouter.java:201-225` `[FABRICATED FILE]` | Reviewed |

**Process for adding a rule:**
1. Ask Claude: *"Extract business rules from `<module>`, trace each to source lines, format as table rows."*
2. Review with the domain owner.
3. Append the validated rule here with a Rule ID.
4. Write a test case in `tests/business-rules/` referencing the Rule ID.

**Dead/conflicting rules to investigate:** `[EXAMPLE — fabricated]`
- `BillingEngine.java:512-540` — branch has not executed since 2017 (per logs); confirm and remove
- `AccountValidator.java:120` vs `:135` — overlap; needs domain owner ruling

---

## 5. Known Issues & Technical Debt `[EXAMPLE]`

- Field injection (`@Autowired` on fields) used throughout — migrate to constructor injection during refactor
- `application.properties` has grown to 2,400+ lines — modularize per bounded context
- No test coverage for `txn.batch.*` (the hot path); **add characterization tests BEFORE touching**
- Oracle-specific SQL (`CONNECT BY`, `(+)` joins) — flag for rewrite when migrating to PostgreSQL
- DB connection pool exhausts under > 800 concurrent users; root cause not yet isolated
- Hardcoded credentials in `LegacyMQClient.java:34` `[FABRICATED FILE]` — rotate and externalize on first touch

---

## 6. Modernization Strategy `[EXAMPLE]`

### Phase 1 — Discovery & Knowledge Extraction *(Q1, in progress)*
- ☑ Inventory all endpoints, batch jobs, scheduled tasks
- ☑ Map dependency graph (module → module, module → DB table)
- ☐ Extract business rules from `billing.*` and `compliance.*` (highest regulatory risk)
- ☐ Generate API documentation for all 57 endpoints into `docs/api/`

### Phase 2 — Strangler Pattern Extraction *(Q2–Q3)*
- ☐ Extract `compliance.screening` as standalone service (lowest coupling, high audit value)
- ☐ Extract `customer.preferences` (read-mostly, isolated data)
- ☐ Route via Spring Cloud Gateway; legacy remains source of truth

### Phase 3 — Core Modernization *(Q4 onwards)*
- ☐ Upgrade Java 8 → 21 in extracted services
- ☐ Migrate `account.*` data layer to PostgreSQL with shadow-write
- ☐ Decompose `TransactionService.processBatch()` (hardest module, last)

### Out of scope (do not touch)
- JSP/Struts frontend (separate replatforming track)
- Mainframe-hosted GL system (vendor-owned)

---

## 7. Workflow Rules for Claude Code

> The rules in this section are general best practices and **safe to keep as-is** for most legacy modernization projects. Adjust thresholds (line counts, etc.) to your team's standards.

### When analyzing legacy code
- Read the entire module before suggesting changes; do not assume from the file name
- For any function > 100 lines, summarize its purpose, inputs, outputs, and side effects FIRST
- Identify implicit state (statics, ThreadLocals, instance fields mutated mid-method) and call it out
- If business logic seems unusual, flag it — do not "fix" it. Unusual logic is usually a rule we have not yet documented

### When refactoring
- **Always** generate characterization tests first; lock in current behaviour before changing it
- Extract one module at a time; keep the legacy version compiling and deployable throughout
- Constructor injection, not field injection, on every new or touched class
- No method longer than 50 lines in new code; no class longer than 300 lines
- Run `mvn verify` (full test + integration) before declaring complete; **single tests for speed during iteration**

### When generating documentation
- Output Markdown to `docs/` mirroring the package structure
- Every endpoint doc must include: purpose, request/response shape, side effects, error semantics, dependencies
- Cite source lines (`File.java:LINE`) for every claim about behaviour

### What Claude must NOT do `[EXAMPLE — adapt to your modules]`
- Do not modify `compliance.*` without an explicit domain-owner sign-off comment in the PR
- Do not change schemas in `db/migrations/` without a corresponding rollback script
- Do not rewrite `BillingEngine` end-to-end in one PR — extract rules first, modernize per rule
- Do not infer regulatory intent from code comments alone — comments are unreliable; rules table is authoritative
- Do not suggest microservices decomposition without referencing the boundaries in section 3

---

## 8. Developer Quick-Start

> First time using Claude Code on this repo? This section gets you productive in under an hour.
> The example commands below assume the CoreLedger stack; adapt to your environment.

### Setup (one-time) `[EXAMPLE]`
1. Install Claude Code via your team's package manager
2. Authenticate with your enterprise SSO
3. Clone the repo: `git clone <REPO_URL> && cd coreledger`
4. From the repo root, run a smoke test:
   ```bash
   claude "Explain the architecture of this codebase in 10 bullets"
   ```
5. If the output references `customer.*`, `account.*`, `txn.*`, `billing.*`, `compliance.*`, and `batch.*` from section 3 of this file, your context is loading correctly.

### Your first session
- **Always launch Claude Code from the repo root** so it picks up this CLAUDE.md
- **Start narrow:** pick ONE module (e.g., `billing.fees`), not the whole codebase
- **End each session with:**
  > *"Summarise what we did and update CLAUDE.md if anything should be persisted."*

### What "good context loading" looks like `[EXAMPLE]`
- Claude references Java 8 (not Java 21) as the current production version unprompted
- Claude uses domain terms like "grandfathered customer" and "EOD" from the glossary correctly
- Claude refuses to suggest changes to `compliance.*` without flagging the domain-owner requirement

If any of the above fails, CLAUDE.md isn't loading. Check that:
- You're in the repo root (`/coreledger`, not `/coreledger/modules/billing`)
- The file is named exactly `CLAUDE.md` (case-sensitive on Linux build agents)
- Imported files in section 16 actually exist at the paths listed

---

## 9. Prompt Library

> Copy-paste these. Customize the bracketed parts. These patterns are tested on the CoreLedger codebase.

### Discovery prompts

**Map a module:**
> *"Analyze the `billing.fees` module. For each public class and method: callers across the codebase, database tables touched (look for `@Query`, JdbcTemplate calls, and Hibernate entities), shared mutable state, and external service calls. Flag anything tightly coupled to other modules — especially anything that reaches into `compliance.*`."*

**Find dead code:**
> *"List functions in `billing.fees` that are never called from production code paths. Distinguish: completely dead, only-called-from-tests, only-called-from-deprecated-SOAP-endpoints (those in the `legacy-soap` package). Cite line numbers for each."*

**Trace a request:**
> *"Walk me through what happens when `POST /v2/account/{id}/payment` is invoked. Start at the controller. List every method called in order, every DB query executed, every IBM MQ message published, and every side effect (cache writes, audit log entries, metric counters)."*

### Business rule extraction `[EXAMPLE]`

**Extract rules:**
> *"Extract every business rule embedded in `BillingEngine.java` into a numbered table. For each rule include: plain-language description, source `File.java:LINE-RANGE`, special cases or exceptions (e.g., grandfathered customers), and a confidence level (High/Medium/Low). Flag rules that look contradictory to others in the same module — for example, two different late-fee calculations on different branches."*

**Compare two implementations:**
> *"`FeeCalculatorV1` and `FeeCalculatorV2` both calculate late fees. Compare them and list every behavioral difference, with source line references. Flag which differences look intentional (e.g., V2 adds rounding) vs. likely bugs (e.g., V2 silently drops the minimum-fee floor)."*

### Refactoring prompts

**Pre-refactor safety:**
> *"Before I refactor `BillingEngine.calculateLateFee()`, generate JUnit 5 characterization tests that lock in current behavior. Cover: null inputs, zero balance, balance exactly at min/max thresholds, grandfathered-customer path, weekend-vs-weekday differences if any, and the exception paths. Tests should fail if I accidentally change the contract."*

**Safe extract:**
> *"Extract `LateFeeCalculator` from `BillingEngine` into a new class `billing.fees.LateFeeCalculator`. Preserve every observable behavior. List all callers that will need to update their imports. Do NOT change method signatures. Use constructor injection on the new class."*

**Modernize incrementally:**
> *"Convert `BillingEngine` from field injection (`@Autowired` on fields) to constructor injection. Keep the public API identical. Show me the diff before making changes."*

### Documentation prompts

**API endpoint doc:**
> *"For `POST /v2/account/{id}/payment`, generate documentation including: purpose, request/response shape with examples, authentication requirements (OAuth scope), side effects (IBM MQ messages published, audit-log entries, downstream service calls), error semantics (every HTTP status code and what triggers it), and rate limits. Cite source `File.java:LINE` for every claim about behavior."*

**Module README:**
> *"Generate a README.md for the `billing.fees` directory: what it does, its public API, how to run its tests (`mvn -pl modules/billing.fees test`), its dependencies on other modules, and any non-obvious gotchas — especially around the grandfathered-customer exemption logic and the Oracle-specific SQL."*

### Review prompts

**Self-review before PR:**
> *"Review the changes I have staged. Flag: violations of conventions in CLAUDE.md section 2 (BigDecimal rounding, UTC timestamps, audit columns, transaction demarcation), missing characterization tests, hardcoded values that should be in `application.properties`, security issues (especially anything that touches `LegacyMQClient`), and any business rule from section 4 (BR-001 through BR-004) that this change touches."*

---

## 10. Daily Workflow Patterns

### The 30-minute discovery loop
1. Pick ONE legacy module you don't fully understand
2. Run the "Map a module" prompt from section 9
3. Read Claude's output. For each surprise, ask:
   > *"Show me the exact code that does X."*
4. Update CLAUDE.md (sections 3, 4, 5, or 14) with anything worth persisting

### The "before-refactor" ritual
1. Generate characterization tests first using the prompt in section 9
2. Run them: `mvn -pl modules/<module> test` — confirm they pass against unchanged code
3. Refactor with Claude's help, one method at a time
4. Re-run tests — confirm they still pass
5. Commit with `[ai-assisted]` tag (see commit conventions in section 12)

### The end-of-session habit
At the end of each Claude Code session, run:
> *"Summarize what we learned this session that should be persisted in CLAUDE.md."*

Review the summary. Append validated items to the relevant section. This keeps CLAUDE.md a living document instead of a stale artefact.

### Example session walkthrough `[EXAMPLE]`

A realistic 45-minute session extracting rules from a billing module:

```
You:    "Map the billing.fees module."

Claude: [returns list of 12 public methods, 4 DB tables, 2 cross-module dependencies]

You:    "Tell me more about calculateLateFee. Walk through every branch."

Claude: [walks through the method, identifies 7 distinct rules]

You:    "Extract those 7 rules as a numbered table for our business analyst."

Claude: [returns table with plain-language descriptions, source lines, confidence levels]

You:    "Now generate characterization tests covering all 7 rules."

Claude: [generates JUnit 5 test class]

You:    "Run the tests."

You:    [verify they pass against unchanged code]

You:    "Summarize this session for CLAUDE.md."

Claude: [returns: "Add BR-005 through BR-011 to section 4. Flag the deprecated
        rebate logic at line 290 as 'dead since 2018' in section 5..."]

You:    [review summary, paste validated items into CLAUDE.md, commit]
```

### When Claude gets stuck

| Symptom | Likely cause | Fix |
|---|---|---|
| Hallucinated file paths or method names | Context window full | Start a new session; reload CLAUDE.md |
| Suggests Java 21 features in production code | Section 2 not being respected | Add explicit reminder: "production runs Java 8" |
| Suggests JPA when codebase uses MyBatis | Section 2 too vague | List actual ORM and version |
| Refuses to touch `compliance.*` | Section 7 prohibition firing correctly | Confirm with domain owner, then proceed with sign-off in PR |
| Cites line numbers that don't exist | File was modified since context loaded | Ask: "Show me the exact code at that line right now" |
| Confidently invents config keys | Did not read `application.properties` | Provide the file explicitly: `@application.properties` |

---

## 11. Code Review Checklist for AI-Assisted PRs

> Use this on every PR tagged `[ai-assisted]`. Reviewers verify these explicitly. Adapted to the CoreLedger conventions.

**Behavior preservation**
- [ ] Characterization tests exist in `src/test/java/.../characterization/` and pass against pre-change code
- [ ] Tests still pass after change (no contract changes)
- [ ] If any test was modified or removed, the PR description explains why

**Business rules** `[EXAMPLE]`
- [ ] If this PR touches `billing.*` or `compliance.*`, the affected BR-IDs are listed in the PR description
- [ ] Domain owner from section 15 is tagged as reviewer for the affected rules
- [ ] Any new rule discovered is added to section 4 with `Pending review` status

**Hallucination guard rails**
- [ ] Every `File.java:LINE` cited in PR description has been verified to exist
- [ ] No invented Spring annotations, library methods, or config keys
- [ ] No fabricated metrics in commit messages (e.g., "improved performance by 40%" without benchmark)

**Conventions** `[EXAMPLE]`
- [ ] Monetary values use `BigDecimal` with `HALF_UP`, scale 4
- [ ] Timestamps stored as UTC; no `Date`, only `Instant` or `LocalDateTime` with explicit zone
- [ ] Audit columns present on any new table
- [ ] No `compliance.*` → `billing.*` imports (forbidden per section 3)
- [ ] Schema changes in `db/migrations/` have a corresponding `Vxxx__rollback.sql`

**Scope**
- [ ] One module modernized at a time (not multiple in one PR)
- [ ] `mvn verify` passes; legacy WAR still deploys to WebSphere

---

## 12. Git & PR Conventions for AI-Assisted Work `[EXAMPLE]`

### Commit message format
```
<type>(<scope>): <short description>

[ai-assisted] <prompt-summary-or-omit>

<longer body if needed>

Refs: BR-XXX, BR-YYY  (if business rules touched)
```

**Examples:**
```
refactor(billing): extract LateFeeCalculator from BillingEngine

[ai-assisted] Strangler extraction with characterization tests

Refs: BR-001, BR-002
```

```
docs(api): regenerate endpoint catalogue for customer module

[ai-assisted] Doc generation from source — 23 endpoints documented
```

```
test(billing): add characterization tests for grandfathered fees

[ai-assisted] Lock in pre-refactor behavior for BR-002

Refs: BR-002
```

### Branch naming
- `ai/<jira-ticket>-<short-desc>` for AI-assisted work
- Example: `ai/CORE-4521-extract-late-fee-calculator`
- Easier filtering, easier metrics on AI-assisted velocity

### PR template additions
Add these fields to `.github/pull_request_template.md` for `[ai-assisted]` PRs:

```markdown
## AI-Assisted Work Disclosure

**Prompt(s) used:**
<brief summary — e.g., "Map module, extract rules, generate characterization tests">

**Verification done beyond `mvn verify`:**
<e.g., "Manually traced the 3 SQL queries Claude generated against the schema in DBeaver">

**Business rules touched:**
<list BR-IDs from CLAUDE.md section 4, or "None">

**CLAUDE.md updated?**
<Yes / No — and which sections>
```

---

## 13. Commands & Build `[EXAMPLE — Maven-specific; adapt to your build tool]`

```bash
# Full build (slow, ~12 min)
mvn clean verify

# Single module (fast iteration)
mvn -pl modules/billing verify

# Run characterization test suite only
mvn -pl modules/<module> -Dtest='*CharacterizationTest' test

# Generate dependency graph
mvn dependency:tree -Doutput=docs/deps.txt

# Lint (Checkstyle + SpotBugs)
mvn checkstyle:check spotbugs:check

# Claude Code helpers (if your team uses any wrappers)
# Example: a wrapper that auto-loads sub-CLAUDE.md files for the target module
./scripts/claude-module billing
```

---

## 14. Glossary (Domain Terms) `[EXAMPLE — replace with terms specific to your domain]`

- **Grandfathered customer** — account opened before 2010; exempt from several fee rules
- **EOD** — End of Day; nightly batch window 22:00–04:00 IST
- **GL** — General Ledger; the mainframe-hosted accounting system (read-only from our side)
- **Hold** — A compliance-triggered freeze on a transaction; not a status, a separate table
- **Settlement window** — T+1 for domestic, T+2 for cross-border; affects scheduler timing

---

## 15. People & Ownership `[EXAMPLE — all names fabricated]`

- **Modernization lead:** `<name>`
- **Domain owner — Billing:** `<name>` (final say on BR-001 through BR-099)
- **Domain owner — Compliance:** `<name>` (final say on BR-200 through BR-299)
- **Principal engineer (legacy):** `<name>` — joined 2007, the human source of truth; escalate ambiguities here
- **DevOps:** `<name>` — owns CI/CD, deployment windows
- **AI tooling champion:** `<name>` — owns this CLAUDE.md, prompt library, and team training

---

## 16. Additional Instructions (imported files)

@docs/architecture-decisions.md
@docs/api/README.md
@.claude/skills/refactor-module.md
@.claude/skills/extract-business-rules.md

---

*Last reviewed: `<DATE>` by `<NAME>`. Review quarterly. Prune anything that has not been touched in 90 days.*

---

> ⚠️ **REMINDER**: This file contains illustrative example content. Before using on a real system, replace every `[EXAMPLE]` value, remove the BR-001 through BR-004 sample rules, and strip both warning banners.

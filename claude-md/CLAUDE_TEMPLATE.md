# CLAUDE.md — Legacy Modernization Playbook

> Persistent project memory for AI-assisted modernization.
> Checked into Git. Owned by the modernization team. Maintained like production code.

This file is read by Claude Code at the start of every session. Keep it concise, current, and high-signal. For each line, ask: *"Would removing this cause Claude to make mistakes?"* If not, cut it.

> **HOW TO USE THIS TEMPLATE**
> Replace every `<PLACEHOLDER>` and `[...]` with your real values. Delete any section that does not apply to your system. Commit to Git and review quarterly.

---

## 1. Project Context

**System name:** `<INTERNAL_NAME>`
**Domain:** `<BUSINESS_DOMAIN — e.g., the business area this system supports>`
**Criticality:** `<TIER — Tier-1 / Tier-2 / Tier-3>` — supports `<WHAT % OF REVENUE OR TRANSACTIONS>`
**Repository:** `<GIT_URL>`
**First commit:** `<YEAR>` • **Team history:** `<e.g., original team disbanded; current team is the second handover>`
**Lines of code (approx):** `<COUNT BY LANGUAGE>`

---

## 2. Stack & Environment

### Languages & Frameworks
- **Backend:** `<LANGUAGE + VERSION>` `<FRAMEWORK + VERSION>` `<ORM>`
- **Database:** `<DB + VERSION>` (target: `<MIGRATION_TARGET if applicable>`)
- **Messaging:** `<QUEUE + VERSION>` (target: `<MIGRATION_TARGET if applicable>`)
- **Build:** `<TOOL + VERSION>`, `<MODULE_COUNT>` modules
- **Frontend:** `<STACK>` (in scope / out of scope for this modernization)

### Conventions (NEVER violate)
> List the rules that, if broken, would cause silent production issues. Be specific.
- `<e.g., money type and rounding mode>`
- `<e.g., timestamp storage and timezone rules>`
- `<e.g., naming convention for DB vs. code>`
- `<e.g., mandatory audit columns on every table>`
- `<e.g., where transactions are demarcated>`

### Runtime
- **Current:** `<APP_SERVER + VERSION>` `<JDK/RUNTIME + VERSION>`
- **Target:** `<MODERNIZED_RUNTIME>`
- `<Any runtime constraints Claude must not violate — e.g., JDK version available in production>`

---

## 3. Architecture Overview

- **Pattern:** `<e.g., monolithic 3-tier, modular monolith, distributed monolith>`
- **Endpoints inventory:** `<COUNT BY PROTOCOL — REST, SOAP, gRPC, etc.>`
- **Bounded contexts (discovered, NOT designed):**
  - `<context-name>` — `<one-line responsibility>`
  - `<context-name>` — `<one-line responsibility>`
  - `<context-name>` — `<one-line responsibility>`
- **High-coupling hotspots (modernize LAST):**
  - `<file/class>` — `<why it is high-risk>`
  - `<file/class>` — `<why it is high-risk>`
- **Forbidden dependencies:**
  - `<context-A>` must never depend on `<context-B>` — `<reason: regulatory / architectural / data-integrity>`

---

## 4. Business Rules Registry

> Critical rules extracted from the legacy code. Every rule is the contract for the modernized version. **Never** modernize a module without first extracting and reviewing its rules with the business.

| Rule ID | Module | Description | Source | Status |
|---------|--------|-------------|--------|--------|
| BR-001 | `<module>` | `<plain-language statement of the rule>` | `<File:LINE-RANGE>` | `Pending / Reviewed / Signed off` |
| BR-002 | `<module>` | `<plain-language statement of the rule>` | `<File:LINE-RANGE>` | `Pending / Reviewed / Signed off` |

**Process for adding a rule:**
1. Ask Claude: *"Extract business rules from `<module>`, trace each to source lines, format as table rows."*
2. Review with the domain owner listed in section 15.
3. Append the validated rule here with a Rule ID.
4. Write a test case in `tests/business-rules/` referencing the Rule ID.

**Dead or conflicting rules to investigate:**
- `<File:LINE>` — `<observation, e.g., branch has not executed since YYYY per logs>`
- `<File:LINE>` vs `<File:LINE>` — `<overlap or contradiction; needs domain owner ruling>`

---

## 5. Known Issues & Technical Debt

> Things Claude should know about before suggesting changes. Be specific.
- `<known anti-pattern present throughout the codebase>`
- `<file or config that has grown unmanageable>`
- `<modules with no test coverage — flag for characterization tests first>`
- `<DB-specific or vendor-specific code that needs careful handling on migration>`
- `<known performance or scaling issues>`
- `<security debt: hardcoded credentials, deprecated crypto, etc.>`

---

## 6. Modernization Strategy

### Phase 1 — Discovery & Knowledge Extraction `<TIMELINE>`
- ☐ `<deliverable>`
- ☐ `<deliverable>`

### Phase 2 — Strangler Pattern Extraction `<TIMELINE>`
- ☐ Extract `<module>` as standalone service — rationale: `<why this one first>`
- ☐ `<deliverable>`

### Phase 3 — Core Modernization `<TIMELINE>`
- ☐ `<deliverable>`
- ☐ `<deliverable>`

### Out of scope (do not touch)
- `<system or module owned by another team>`
- `<frozen vendor component>`

---

## 7. Workflow Rules for Claude Code

### When analyzing legacy code
- Read the entire module before suggesting changes; do not assume from the file name
- For any function exceeding `<LINE_THRESHOLD>` lines, summarize purpose, inputs, outputs, and side effects first
- Identify implicit state (statics, ThreadLocals, instance fields mutated mid-method) and call it out
- If business logic seems unusual, FLAG it — do not "fix" it. Unusual logic is usually a rule not yet documented

### When refactoring
- **Always** generate characterization tests first; lock in current behaviour before changing it
- Extract one module at a time; keep the legacy version compiling and deployable throughout
- `<your team's coding conventions — e.g., constructor injection only, max method length, max class length>`
- Run `<full verify command>` before declaring complete; use `<single-test command>` for iteration

### When generating documentation
- Output Markdown to `docs/` mirroring the package structure
- Every endpoint doc must include: purpose, request/response shape, side effects, error semantics, dependencies
- Cite source lines (`File:LINE`) for every claim about behaviour

### What Claude must NOT do
- Do not modify `<sensitive module>` without an explicit `<role>` sign-off in the PR
- Do not change schemas in `<migration-dir>` without a corresponding rollback script
- Do not rewrite `<critical module>` end-to-end in one PR — extract rules first, modernize per rule
- Do not infer regulatory intent from code comments — comments are unreliable; the rules table in section 4 is authoritative
- Do not propose architectural changes without referencing the bounded contexts in section 3

---

## 8. Developer Quick-Start

> First time using Claude Code on this repo? This section gets you productive in under an hour.

### Setup (one-time)
1. Install Claude Code: `<install command>`
2. Authenticate: `<auth command>`
3. From the repo root, run a smoke test: `claude "Explain the architecture of this codebase in 10 bullets"`
4. If the output references modules from section 3 of this file, your context is loading correctly.

### Your first session
- Always launch Claude Code from the repo root so it picks up this CLAUDE.md
- Start narrow: pick ONE module, not the whole codebase
- End each session with: *"Summarise what we did and update CLAUDE.md if anything should be persisted."*

### What "good context loading" looks like
- Claude references your stack versions from section 2 unprompted
- Claude uses your domain glossary terms (section 14) correctly
- Claude refuses to touch modules listed in section 7's "must NOT do" list

If any of the above fails, CLAUDE.md isn't loading. Check you're in the repo root and the file is named exactly `CLAUDE.md` (case-sensitive on Linux).

---

## 9. Prompt Library

> Copy-paste these. Customize the bracketed parts. Tested patterns that work well on this codebase.

### Discovery prompts

**Map a module:**
> *"Analyze the `<module-name>` module. For each public class and method: callers across the codebase, database tables touched, shared mutable state, and external service calls. Flag anything tightly coupled to other modules."*

**Find dead code:**
> *"List functions in `<module>` that are never called from production code paths. Distinguish: dead, only-called-from-tests, only-called-from-deprecated-endpoints."*

**Trace a request:**
> *"Walk me through what happens when `<endpoint-or-event>` is invoked. Start at the entry point. List every method called in order, every DB query executed, every external call made, and every side effect."*

### Business rule extraction

**Extract rules:**
> *"Extract every business rule embedded in `<module>` into a numbered table. For each rule include: plain-language description, source `File:LINE`, special cases or exceptions, and a confidence level (High/Medium/Low). Flag rules that look contradictory to others in the same module."*

**Compare two implementations:**
> *"`<ModuleA>` and `<ModuleB>` both calculate `<thing>`. Compare them and list every behavioral difference, with source line references. Flag which differences look intentional vs. likely bugs."*

### Refactoring prompts

**Pre-refactor safety:**
> *"Before I refactor `<method>`, generate characterization tests that lock in current behavior — including null inputs, boundary values, exception paths, and any side effects. Tests should fail if I accidentally change the contract."*

**Safe extract:**
> *"Extract `<class-or-method>` into a new module `<new-module-name>`. Preserve every observable behavior. List all callers that will need to update their imports. Do NOT change method signatures."*

**Modernize incrementally:**
> *"Convert `<class>` from field injection to constructor injection. Keep the public API identical. Show me the diff before making changes."*

### Documentation prompts

**API endpoint doc:**
> *"For `<endpoint>`, generate documentation including: purpose, request/response shape with examples, authentication requirements, side effects, downstream dependencies, error semantics, and rate limits. Cite source `File:LINE` for every claim about behavior."*

**Module README:**
> *"Generate a README.md for the `<module>` directory: what it does, its public API, how to run its tests, its dependencies, and any non-obvious gotchas a new engineer should know."*

### Review prompts

**Self-review before PR:**
> *"Review the changes I have staged. Flag: violations of conventions in CLAUDE.md section 2, missing tests, hardcoded values that should be config, security issues, and any business rule from section 4 that this change touches."*

---

## 10. Daily Workflow Patterns

### The 30-minute discovery loop
1. Pick ONE legacy module you don't fully understand
2. Run the "Map a module" prompt
3. Read Claude's output. For each surprise, ask: *"Show me the code that does X."*
4. Update CLAUDE.md with anything worth persisting

### The "before-refactor" ritual
1. Generate characterization tests first
2. Run them — confirm they pass against unchanged code
3. Refactor with Claude's help
4. Re-run tests — confirm they still pass
5. Commit with `[ai-assisted]` tag (see commit conventions in section 12)

### The end-of-session habit
At the end of each Claude Code session, run:
> *"Summarize what we learned this session that should be persisted in CLAUDE.md."*

Review the summary. Append validated items to the relevant section. This keeps CLAUDE.md a living document instead of a stale artefact.

### When Claude gets stuck

| Symptom | Likely cause | Fix |
|---|---|---|
| Hallucinated file paths or method names | Context window full | Start a new session; reload CLAUDE.md |
| Suggests deprecated APIs | Section 2 missing target versions | Update the Stack section |
| Ignores your conventions | Conventions too vague | Be specific: replace "use modern patterns" with actual rule |
| Refuses a reasonable change | Over-cautious from section 7 prohibitions | Reframe with the specific context that makes it safe |
| Confidently wrong about behavior | Did not actually read the file | Ask "show me the exact code you're referencing" |

---

## 11. Code Review Checklist for AI-Assisted PRs

> Use this on every PR tagged `[ai-assisted]`. Reviewers verify these explicitly.

**Behavior preservation**
- [ ] Characterization tests exist and pass against pre-change code
- [ ] Tests still pass after change (no contract changes)
- [ ] If any test was modified or removed, the PR description explains why

**Business rules**
- [ ] If this PR touches a module in section 4 of CLAUDE.md, the affected Rule IDs are listed in the PR description
- [ ] Domain owner from section 15 is tagged as reviewer for the affected rules

**Hallucination guard rails**
- [ ] Every file path and line number cited in PR description has been verified
- [ ] No invented API calls, library methods, or config keys
- [ ] No fabricated metrics or "achievement" claims in commit messages

**Conventions**
- [ ] Changes follow section 2 conventions (rounding, timestamps, naming, audit columns)
- [ ] No prohibited cross-module dependencies (section 3)
- [ ] Schema migrations have rollback scripts

**Scope**
- [ ] One module modernized at a time (not multiple in one PR)
- [ ] Legacy system still compiles and deploys after this change

---

## 12. Git & PR Conventions for AI-Assisted Work

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

[ai-assisted] Doc generation from source
```

### Branch naming
- `ai/<jira-ticket>-<short-desc>` for AI-assisted work
- Easier filtering, easier metrics on AI-assisted velocity

### PR template additions
Add these fields to your PR template for `[ai-assisted]` PRs:
- **Prompt(s) used:** Brief summary of the prompt(s) given to Claude
- **Verification done:** What the human verified beyond test pass (e.g., "manually traced the SQL queries Claude generated")
- **Business rules touched:** List of BR-IDs from section 4
- **CLAUDE.md updated?** Yes/No — and where

---

## 13. Commands & Build

```bash
# Full build
<FULL_BUILD_COMMAND>

# Single module (fast iteration)
<SINGLE_MODULE_COMMAND>

# Run characterization tests only
<CHARACTERIZATION_TEST_COMMAND>

# Generate dependency graph
<DEP_GRAPH_COMMAND>

# Lint and static analysis
<LINT_COMMAND>

# Claude Code-specific helpers (if you have any)
# e.g., scripts that pre-load context, or wrap claude with team defaults
<CUSTOM_CLAUDE_HELPER_COMMANDS>
```

---

## 14. Glossary (Domain Terms)

> Terms a new engineer (or Claude) would not understand from code alone. Keep entries short.

- **`<TERM>`** — `<one-line definition>`
- **`<TERM>`** — `<one-line definition>`
- **`<TERM>`** — `<one-line definition>`

---

## 15. People & Ownership

- **Modernization lead:** `<NAME>`
- **Domain owner — `<context>`:** `<NAME>` (final say on `<rule-id range>`)
- **Domain owner — `<context>`:** `<NAME>` (final say on `<rule-id range>`)
- **Principal engineer (legacy):** `<NAME>` — `<years on the system>`; escalate ambiguities here
- **DevOps / Platform:** `<NAME>` — owns CI/CD, deployment windows
- **AI tooling champion:** `<NAME>` — owns this CLAUDE.md, prompt library, and team training

---

## 16. Additional Instructions (imported files)

> Use `@path/to/file` syntax to import additional context Claude should read.

@docs/architecture-decisions.md
@docs/api/README.md
@.claude/skills/refactor-module.md
@.claude/skills/extract-business-rules.md

---

*Last reviewed: `<DATE>` by `<NAME>`. Review quarterly. Prune anything not touched in 90 days.*

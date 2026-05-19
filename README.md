# Claude Code for Modernization of Legacy Applications

> **The biggest value of Claude Code isn't code generation. It's enterprise knowledge extraction.**

A practical perspective on using AI coding tools to modernize legacy enterprise systems — including the article, downloadable CLAUDE.md templates, and a reproducible 30-minute demo.

[![License: MIT](https://img.shields.io/badge/Code-MIT-blue.svg)](LICENSE)
[![License: CC BY 4.0](https://img.shields.io/badge/Content-CC%20BY%204.0-lightgrey.svg)](LICENSE-CONTENT)

---

## What's in this repo

| What | Where | For who |
|---|---|---|
| 📄 **The article** (5-min read) | [`article/README.md`](./article/README.md) | Engineering leaders, CIOs, architects |
| 📋 **CLAUDE.md templates** | [`claude-md/`](./claude-md/) | Developers, modernization teams |
| 🐾 **Hands-on demo** (Spring PetClinic, 30 min) | [`demo/DEMO.md`](./demo/DEMO.md) | Developers wanting to try this themselves |

---

## The TL;DR

Most enterprise companies quietly run on legacy monolithic applications understood by a handful of soon-to-retire developers. The default modernization playbook — hire an SI, spend 18 months on "discovery," watch the budget triple — fails 74% of the time.

The root cause is never typing speed. It's a failure of understanding.

Claude Code's superpower isn't writing code. **It's reading it.** And on a legacy codebase, that's the unlock.

Read the full article: [`article/README.md`](./article/README.md)

Also published on LinkedIn: [link to your LinkedIn Article]

---

## Quick-start: Drop a CLAUDE.md into your repo

The single highest-leverage thing you can do today:

1. Copy [`claude-md/CLAUDE_TEMPLATE.md`](./claude-md/CLAUDE_TEMPLATE.md) into the root of your project as `CLAUDE.md`
2. Fill in the placeholders with your stack, conventions, and bounded contexts
3. Commit it
4. Every Claude Code session now starts with your context loaded

See [`claude-md/README.md`](./claude-md/README.md) for details on the three templates.

---

## Try the demo (30 minutes)

Want to see this workflow on real code before adopting it?

```bash
git clone https://github.com/spring-projects/spring-petclinic.git
cd spring-petclinic
# Copy CLAUDE_petclinic.md from this repo into spring-petclinic/ as CLAUDE.md
claude
```

Then follow the five prompts in [`demo/DEMO.md`](./demo/DEMO.md).

---

## About

Written by [Prakash Sridharan](https://www.linkedin.com/in/prakashsridharan-software-dev/) — engineering leader with 24 years in enterprise software, 3 U.S. patents, and an active interest in how AI changes the economics of legacy modernization.

If this resonated, the easiest way to support it is to ⭐ this repo and share the article.

---

## License

- **Code and templates** (the CLAUDE.md files, DEMO.md, scripts) — [MIT License](./LICENSE)
- **Article content and images** — [Creative Commons Attribution 4.0](./LICENSE-CONTENT)

You can use, adapt, and share everything in this repo. Attribution appreciated.
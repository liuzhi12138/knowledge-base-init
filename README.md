# knowledge-base-init

> AI Agent Skill — Generate a complete AI Coding knowledge base for any project.

## What it does

Scans source code, extracts business rules, and creates structured documentation with built-in quality verification.

Designed for AI coding agents (Qoder, Codex, etc.) to quickly build context awareness of unfamiliar codebases.

## Structure

```
.
├── SKILL.md                    # Skill definition (instructions & workflow)
└── assets/
    ├── examples.md             # Usage examples
    └── templates/              # Output document templates
        ├── agents-template.md
        ├── business-rules-template.md
        ├── governance-gaps-template.md
        ├── index-template.md
        ├── legacy-and-side-paths-template.md
        └── source-facts-template.md
```

## Install

```bash
# Using skill-installer (if available)
gh repo clone liuzhi12138/knowledge-base-init ~/.qoder/skills/personal/knowledge-base-init
```

Or copy the `SKILL.md` and `assets/` directory to your agent's skills directory.

# AI Coding Agent Playbook Examples

A collection of ready-to-use templates and examples for customizing AI coding agents. Includes configurations for both **GitHub Copilot** (VS Code) and **Claude Code** (CLI). Use these as starting points for your own projects.

---

## GitHub Copilot

### What Are Copilot Playbooks?

Copilot playbooks (also called customization files) let you tailor Copilot's behavior to your project, team, and workflows. They live in your repo and are version-controlled alongside your code.

### Customization Primitives

| Primitive | File Type | Location | Purpose |
|-----------|-----------|----------|---------|
| **Workspace Instructions** | `copilot-instructions.md` or `AGENTS.md` | `.github/` or root | Always-on project-wide guidelines |
| **File Instructions** | `*.instructions.md` | `.github/instructions/` | On-demand or file-pattern-matched guidance |
| **Prompts** | `*.prompt.md` | `.github/prompts/` | Reusable single-task templates (slash commands) |
| **Custom Agents** | `*.agent.md` | `.github/agents/` | Specialized personas with tool restrictions |
| **Skills** | `SKILL.md` | `.github/skills/<name>/` | Multi-step workflows with bundled assets |
| **Hooks** | `*.json` | `.github/hooks/` | Deterministic lifecycle automation |

## Project Structure

### Copilot (`.github/`)

```
.github/
├── copilot-instructions.md          # Workspace-wide instructions
├── instructions/
│   ├── react.instructions.md        # React/frontend guidance
│   ├── api-design.instructions.md   # REST API conventions
│   ├── testing.instructions.md      # Testing standards
│   ├── database.instructions.md     # Database/migration rules
│   └── security.instructions.md     # Security practices
├── prompts/
│   ├── generate-tests.prompt.md     # Generate test cases
│   ├── code-review.prompt.md        # Review code for issues
│   ├── create-api-endpoint.prompt.md # Scaffold a REST endpoint
│   ├── refactor.prompt.md           # Refactor selected code
│   └── document-code.prompt.md      # Generate documentation
├── agents/
│   ├── reviewer.agent.md            # Code review specialist
│   ├── architect.agent.md           # Architecture advisor
│   └── debugger.agent.md            # Debugging specialist
├── skills/
│   └── deploy-checklist/
│       ├── SKILL.md                 # Deployment checklist skill
│       └── references/
│           └── checklist.md         # Detailed checklist items
└── hooks/
    └── pre-commit-check.json        # Auto-format on file save
```

### Claude Code (`CLAUDE.md` + `.claude/`)

```
CLAUDE.md                                    # Project-wide instructions (always loaded)
.claude/
├── settings.json                            # Hooks (auto-format on edit)
└── skills/
    ├── reviewer/                            # Senior code reviewer
    │   ├── SKILL.md                         #   Main instructions
    │   ├── reference.md                     #   Severity levels & output format
    │   └── examples.md                      #   Sample review outputs
    ├── architect/                           # Architecture advisor
    │   ├── SKILL.md
    │   ├── reference.md                     #   ADR format & diagram conventions
    │   └── examples.md
    ├── debugger/                            # Debugging specialist
    │   ├── SKILL.md
    │   ├── reference.md                     #   Debugging methodology
    │   └── examples.md
    ├── code-review/                         # Structured review checklist
    │   ├── SKILL.md
    │   ├── reference.md                     #   Checklist categories
    │   └── examples.md
    ├── create-api-endpoint/                 # Scaffold REST endpoints
    │   ├── SKILL.md
    │   ├── reference.md                     #   REST conventions & file structure
    │   └── examples.md
    ├── deploy-checklist/                    # Pre-deployment verification
    │   ├── SKILL.md
    │   ├── reference.md                     #   Full deployment checklist
    │   └── examples.md
    ├── document-code/                       # Generate documentation
    │   ├── SKILL.md
    │   ├── reference.md                     #   JSDoc conventions
    │   └── examples.md
    ├── generate-tests/                      # Generate test cases
    │   ├── SKILL.md
    │   ├── reference.md                     #   Test structure & naming
    │   └── examples.md
    ├── refactor/                            # Code restructuring
    │   ├── SKILL.md
    │   ├── reference.md                     #   Refactoring patterns
    │   └── examples.md
    ├── explain-code/                        # Code walkthroughs
    │   ├── SKILL.md
    │   ├── reference.md                     #   Explanation structure
    │   └── examples.md
    ├── pr-description/                      # PR title & description
    │   ├── SKILL.md
    │   ├── reference.md                     #   PR format template
    │   └── examples.md
    ├── performance-audit/                   # Performance bottlenecks
    │   ├── SKILL.md
    │   ├── reference.md                     #   Common anti-patterns
    │   └── examples.md
    ├── migration-guide/                     # Safe DB migrations
    │   ├── SKILL.md
    │   ├── reference.md                     #   Two-phase approach & patterns
    │   └── examples.md
    └── security-audit/                      # OWASP vulnerability scanning
        ├── SKILL.md
        ├── reference.md                     #   OWASP Top 10 checklist
        └── examples.md
```

## How to Use These Examples

### Copilot

1. **Browse the examples** — Each file includes comments explaining the format and best practices
2. **Copy what you need** — Drop files into your project's `.github/` folder
3. **Customize** — Adapt the content to match your stack, conventions, and workflows
4. **Invoke in VS Code** — Prompts and skills appear when you type `/` in Copilot Chat; agents show in the agent picker

## Quick Start

Copy the entire `.github/` folder into your project root:

```bash
cp -r .github/ /path/to/your/project/.github/
```

Then open your project in VS Code — Copilot will automatically pick up the customization files.

### Claude Code

Copy `CLAUDE.md` to your project root and `.claude/` folder for skills and hooks:

```bash
cp CLAUDE.md /path/to/your/project/
cp -r .claude/ /path/to/your/project/.claude/
```

Then run `claude` in your project directory — it will automatically load `CLAUDE.md`. Use `/skill-name` to invoke skills (e.g., `/reviewer`, `/generate-tests`, `/security-audit`).

## Key Concepts

### Copilot

#### Description is Everything
The `description` field in frontmatter is how Copilot discovers your files. Use the **"Use when..."** pattern with specific keywords:

```yaml
# Good — discoverable
description: "Use when writing React components, hooks, or JSX templates"

# Bad — vague
description: "Frontend tips"
```

### One Concern Per File
Keep each file focused. Don't mix testing guidelines with API design in a single instructions file.

### Show, Don't Tell
Short code examples beat long explanations:

```markdown
# Good
Prefer named exports:
export function MyComponent() { ... }

# Bad
When creating components, you should always use named exports instead of
default exports because they provide better refactoring support and make
imports more explicit across the codebase...
```

### Claude Code

#### CLAUDE.md is Always Loaded
Unlike Copilot's file-pattern-scoped instructions, `CLAUDE.md` at the project root is always loaded. Use clear section headers to indicate when guidelines apply:

```markdown
## API Design Guidelines
Apply when working in `src/api/` or `src/routes/`.
```

You can also place `CLAUDE.md` files in subdirectories — they're loaded when Claude works with files in that directory.

#### Skills Replace Commands
Claude Code skills live in `.claude/skills/<name>/` directories. Each skill is a folder with:

- **`SKILL.md`** (required) — Frontmatter (name, description) + main instructions
- **`reference.md`** (on demand) — Detailed formats, checklists, conventions
- **`examples.md`** (on demand) — Sample inputs and outputs

The `SKILL.md` frontmatter controls behavior:

```yaml
---
name: my-skill
description: What this skill does and when to use it
disable-model-invocation: true  # Only manual /my-skill invocation
---
```

Claude loads `SKILL.md` when relevant and fetches supporting files only when needed, keeping context usage efficient.

> **Note**: `.claude/commands/*.md` files still work and are supported, but skills are the recommended format as they support supporting files and additional frontmatter options.

#### Hooks Live in Settings
Claude Code hooks are configured in `.claude/settings.json` under the `hooks` key, not as separate files.

## Comparison Table

| Concept | Copilot (VS Code) | Claude Code (CLI) |
|---------|-------------------|-------------------|
| Project instructions | `.github/copilot-instructions.md` | `CLAUDE.md` (root) |
| Scoped instructions | `.github/instructions/*.instructions.md` with `applyTo` | Subdirectory `CLAUDE.md` files |
| Reusable prompts | `.github/prompts/*.prompt.md` | `.claude/skills/*/SKILL.md` |
| Specialized agents | `.github/agents/*.agent.md` | `.claude/skills/*/SKILL.md` (persona in role) |
| Multi-step skills | `.github/skills/*/SKILL.md` | `.claude/skills/*/SKILL.md` + supporting files |
| Lifecycle hooks | `.github/hooks/*.json` | `.claude/settings.json` `hooks` section |

## Documentation

### Copilot
- [Custom Instructions](https://code.visualstudio.com/docs/copilot/customization/custom-instructions)
- [Prompt Files](https://code.visualstudio.com/docs/copilot/customization/prompt-files)
- [Custom Agents](https://code.visualstudio.com/docs/copilot/customization/custom-agents)
- [Agent Skills](https://code.visualstudio.com/docs/copilot/customization/agent-skills)
- [Hooks](https://code.visualstudio.com/docs/copilot/customization/hooks)

### Claude Code
- [Claude Code Overview](https://code.claude.com/docs/en/overview)
- [CLAUDE.md Memory Files](https://code.claude.com/docs/en/memory)
- [Skills](https://code.claude.com/docs/en/skills)
- [Hooks](https://code.claude.com/docs/en/hooks)
- [Settings](https://code.claude.com/docs/en/settings)

## License

MIT — Use these examples freely in your own projects.

# AI Coding Agent Playbook Examples

A collection of ready-to-use templates and examples for customizing AI coding agents. Includes configurations for **GitHub Copilot** (VS Code), **Claude Code** (CLI), and **Cursor** (IDE). Use these as starting points for your own projects.

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

---

## Cursor

### What Are Cursor Playbooks?

Cursor playbooks let you customize Agent behavior using rules and skills stored in your repo. Rules provide always-on or context-triggered guidance, while skills package multi-step workflows that the agent can invoke automatically or on demand via `/skill-name`.

### Customization Primitives

| Primitive | File Type | Location | Purpose |
|-----------|-----------|----------|---------|
| **Always Rules** | `*.mdc` | `.cursor/rules/` | Always-on project-wide conventions (`alwaysApply: true`) |
| **Glob Rules** | `*.mdc` | `.cursor/rules/` | Triggered when matching files are open (`globs` pattern) |
| **Agent Rules** | `*.mdc` | `.cursor/rules/` | Loaded when the agent decides they're relevant |
| **Skills** | `SKILL.md` | `.cursor/skills/<name>/` | Multi-step workflows with optional scripts, references, and assets |

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
│   ├── document-code.prompt.md      # Generate documentation
│   ├── migrate-delphi.prompt.md     # Migrate Delphi unit/class to .NET
│   ├── migrate-delphi-form.prompt.md    # Convert VCL form to API endpoint
│   └── migrate-delphi-data.prompt.md    # Convert data module to EF Core
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

### Cursor (`.cursor/`)

```
.cursor/
├── rules/
│   ├── coding-standards.mdc              # Always-on .NET coding standards
│   ├── minimal-api.mdc                   # Minimal API patterns (agent decides)
│   ├── ef-migration.mdc                  # EF Core migration rules (agent decides)
│   ├── security.mdc                      # Security guidelines (agent decides)
│   └── test-conventions.mdc              # Test naming & structure (glob: *Tests.cs)
└── skills/
    ├── code-review/                      # Review code for issues
    │   └── SKILL.md
    ├── create-migration/                 # Generate EF Core migrations
    │   └── SKILL.md
    ├── crud-implementation/              # CRUD patterns reference
    │   ├── SKILL.md
    │   └── templates.md                  #   Code templates per layer
    ├── progress-to-dotnet/               # Migrate Progress OpenEdge to .NET
    │   ├── SKILL.md
    │   └── references/
    │       ├── type-mapping.md           #   Progress ABL → C# type mapping
    │       └── examples.md              #   Full before/after examples
    ├── deploy-checklist/                 # Pre-deployment verification
    │   ├── SKILL.md
    │   └── references/
    │       └── checklist.md             #   Detailed checklist items
    ├── ef-core-troubleshoot/             # Diagnose EF Core issues
    │   └── SKILL.md
    ├── generate-api-endpoint/            # Scaffold REST endpoints
    │   └── SKILL.md
    ├── generate-tests/                   # Generate xUnit test cases
    │   └── SKILL.md
    └── implement-crud/                   # Full CRUD vertical slice
        └── SKILL.md
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

### Cursor

Copy the `.cursor/` folder into your project root:

```bash
cp -r .cursor/ /path/to/your/project/.cursor/
```

Then open your project in Cursor — rules load automatically based on their `alwaysApply` / `globs` settings. Skills appear when you type `/` in Agent chat or are applied automatically when the agent determines they're relevant.

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

### Cursor

#### Rules Have Three Modes
Cursor rules use frontmatter to control when they're loaded:

```yaml
# Always loaded — project-wide standards
alwaysApply: true

# Loaded when matching files are open
globs: "**/*Tests.cs, **/Tests/**/*.cs"

# Agent decides based on description (default when no globs/alwaysApply)
alwaysApply: false
```

Write clear `description` fields — the agent uses them to decide relevance for agent-mode rules:

```yaml
# Good — agent knows when to load it
description: "Minimal API endpoint patterns for ASP.NET Core. Use when creating or modifying API endpoints."

# Bad — too vague
description: "API stuff"
```

#### Skills Follow a Consistent Structure
Every Cursor skill uses the same seven sections for predictable, high-quality output:

| Section | Purpose |
|---------|---------|
| **When to Use** | Triggers for relevance matching |
| **Parameters** | Named inputs (required/optional) |
| **Context** | Project architecture background |
| **Task** | Step-by-step procedure |
| **Constraints** | Hard rules and guardrails |
| **Examples** | Concrete input → output pairs |
| **Output** | Expected response format |

Skills with `disable-model-invocation: true` are only invoked explicitly via `/skill-name` (like slash commands). Without this flag, the agent applies them automatically when relevant.

#### Skills Support Bundled Assets
Skills can include `references/`, `scripts/`, and `assets/` directories. The agent loads `SKILL.md` first and fetches supporting files on demand:

```
.cursor/skills/progress-to-dotnet/
├── SKILL.md                    # Main instructions (always loaded)
└── references/
    ├── type-mapping.md         # Loaded when mapping types
    └── examples.md             # Loaded when needing examples
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

| Concept | Copilot (VS Code) | Claude Code (CLI) | Cursor (IDE) |
|---------|-------------------|-------------------|--------------|
| Project instructions | `.github/copilot-instructions.md` | `CLAUDE.md` (root) | `.cursor/rules/*.mdc` (`alwaysApply: true`) |
| Scoped instructions | `.github/instructions/*.instructions.md` with `applyTo` | Subdirectory `CLAUDE.md` files | `.cursor/rules/*.mdc` with `globs` |
| Reusable prompts | `.github/prompts/*.prompt.md` | `.claude/skills/*/SKILL.md` | `.cursor/skills/*/SKILL.md` (`disable-model-invocation: true`) |
| Specialized agents | `.github/agents/*.agent.md` | `.claude/skills/*/SKILL.md` (persona in role) | `.cursor/skills/*/SKILL.md` (persona in role) |
| Multi-step skills | `.github/skills/*/SKILL.md` | `.claude/skills/*/SKILL.md` + supporting files | `.cursor/skills/*/SKILL.md` + `references/`, `scripts/`, `assets/` |
| Lifecycle hooks | `.github/hooks/*.json` | `.claude/settings.json` `hooks` section | — (not yet supported) |

## Documentation

### Copilot
- [Custom Instructions](https://code.visualstudio.com/docs/copilot/customization/custom-instructions)
- [Prompt Files](https://code.visualstudio.com/docs/copilot/customization/prompt-files)
- [Custom Agents](https://code.visualstudio.com/docs/copilot/customization/custom-agents)
- [Agent Skills](https://code.visualstudio.com/docs/copilot/customization/agent-skills)
- [Hooks](https://code.visualstudio.com/docs/copilot/customization/hooks)

### Cursor
- [Rules](https://cursor.com/docs/rules)
- [Agent Skills](https://cursor.com/docs/skills)

### Claude Code
- [Claude Code Overview](https://code.claude.com/docs/en/overview)
- [CLAUDE.md Memory Files](https://code.claude.com/docs/en/memory)
- [Skills](https://code.claude.com/docs/en/skills)
- [Hooks](https://code.claude.com/docs/en/hooks)
- [Settings](https://code.claude.com/docs/en/settings)

## License

MIT — Use these examples freely in your own projects.

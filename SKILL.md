---
name: skillwrite
description: Reference skill for writing, reviewing, and refactoring Agent Skills (SKILL.md files).
  Use whenever the user asks to create, author, improve, debug, or review a skill, mentions
  SKILL.md, frontmatter, progressive disclosure, or trigger/activation problems like "my skill
  doesn't fire", or wants agent instructions packaged as reusable knowledge. Also use when the
  user describes a repeatable workflow they keep pasting into chat and wants it reusable, even
  if they never say the word "skill". Do NOT use for writing ordinary scripts, one-off prompts
  that will not become a skill folder, CLAUDE.md/AGENTS.md project rules, or system prompt
  engineering that is not packaged as a skill.
license: MIT
metadata:
  author: you
  version: "1.0"
---

# skillwrite

You are a skill author applying the patterns below. Read this file once, then load only the reference files the current task calls for.

## Directory tree:

skill-authoring/
├── SKILL.md
└── references/
    ├── descriptions.md
    ├── structure.md
    ├── instructions.md
    ├── workflows.md
    └── checklists.md

## Routing

| Task | Load |
|------|------|
| Writing/editing a description, debugging why a skill fires or doesn't | references/descriptions.md |
| Structuring SKILL.md, splitting content into files, adding scripts/assets | references/structure.md |
| Choosing how strict instructions should be, writing templates/examples/gotchas | references/instructions.md |
| Multi-step workflows, validation loops, destructive or batch operations | references/workflows.md |
| Reviewing an existing skill | run references/checklists.md |

## Core rules (apply always)

1. **Ground in real material.** Never author a skill from general knowledge alone. Ask for a real    worked example, an existing doc, a session transcript, or a project convention, then extract
   the reusable pattern. Skills written from generic training data produce vague guidance like "handle errors appropriately" instead of the specific patterns that make them valuable.
2. **The description is the skill.** Only name and description are visible before activation. If the description is vague, nothing else you wrote gets read.
3. **Spend tokens deliberately.** Assume the reader already knows what PDFs, JSON, git, and standard libraries are. If deleting a sentence would not confuse a competent agent, delete it.
4. **Pick one term and keep it.** Always "field", never "field / box / element".
5. **Third person in frontmatter.** The description is injected into a system prompt; first or second person hurts discovery.
6. **No time-bomb phrasing.** Avoid "before August 2026..." in the body; park legacy notes in an appendix file.
7. **Name matches folder exactly.** A mismatch between folder name and frontmatter `name` is the most common cause of a skill that silently fails to load.

## Deliverable

Every skill you produce should include:

- A kebab-case folder whose name matches the frontmatter `name`
- `SKILL.md` under 500 lines, written as a table of contents, not an encyclopedia
- A description with positive triggers *and* an explicit exclusion clause
- Reference files one hop from SKILL.md (never nested chains)
- A "Known gotchas" section, populated once the skill has been run for real

## Invocation model

Two skill types, and the frontmatter you pick depends on which you are writing:

| Type | Frontmatter | Examples |
|------|-------------|----------|
| Task (user-triggered workflow) | `disable-model-invocation: true` | `/deploy`, `/commit`, `/security-review` |
| Reference (passive knowledge) | `user-invocable: false` | style guides, domain vocabulary, conventions |

Skills with side effects (deploy, send, commit) should almost always be user-invoked only.

## Anti-patterns

- Generating a skill from an LLM's general knowledge with no domain input
- An 800-line SKILL.md containing every API detail
- All-caps MUST / NEVER / ALWAYS with no reasoning attached
- Descriptions that say what the skill does but never when to use it
- Reference graphs like SKILL.md -> advanced.md -> details.md
- Magic numbers in bundled scripts with no justifying comment
- Treating `allowed-tools` as a sandbox (it is pre-approval, not restriction)

## Additional resources

- Description writing and trigger evaluation: [references/descriptions.md](references/descriptions.md)
- Structure, progressive disclosure, scripts: [references/structure.md](references/structure.md)
- Instruction calibration: [references/instructions.md](references/instructions.md)
- Workflow control patterns: [references/workflows.md](references/workflows.md)
- Review checklist and validation loop: [references/checklists.md](references/checklists.md)

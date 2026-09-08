# Structure and Progressive Disclosure

## The three levels

| Level | Content | Cost |
|-------|---------|------|
| L1 Metadata | name + description from every installed skill | always loaded, ~100 tokens each |
| L2 Instructions | full SKILL.md body | loaded only when the skill activates |
| L3 Resources | references/, scripts/, assets/ | loaded or executed only when needed |

Optimize each level for its own job. Note that once a skill's body loads, it tends to stay in
context for the rest of the session, so every line is a recurring cost.

## Standard layout

```text
skill-name/
├── SKILL.md          # Required: metadata + instructions (under 500 lines)
├── scripts/          # Optional: executable code (runs, usually not loaded)
├── references/       # Optional: documentation loaded on demand
└── assets/           # Optional: templates, images, resources
```

## SKILL.md as table of contents

Keep the body lean and point outward. Always say what each linked file contains and when to load
it, otherwise the agent cannot route:

```markdown
## Additional resources
- For complete API details, see [reference.md](reference.md)
- For usage examples, see [examples.md](examples.md)
```

Rules:

- **One hop only.** SKILL.md -> detail.md. Chains like SKILL.md -> advanced.md -> details.md
  increase the chance the agent partially reads the target and misses the rest.
- **TOC at the top of long reference files.** Anything over a few hundred lines should lead with a
  table of contents so the full scope is visible even from a truncated read.
- **Split by domain, not by length.** `reference/finance.md`, `reference/legal.md` beats
  `part1.md`, `part2.md`.

## Bundling scripts

Ship purpose-built helpers in `scripts/` rather than asking the agent to regenerate the same
validator or extractor every run. Generated-from-scratch code is slower, less reliable, and burns
tokens on code the user never sees.

- Be self-contained or document dependencies explicitly. Some runtimes have no network access and
  no package installation; list required packages in SKILL.md and confirm availability.
- Handle common failure modes cleanly: create a missing file with a sensible default, or fail fast
  with a clear message. Do not dump ambiguity back on the agent.
- Justify constants in comments (`# 30s timeout covers slow connections`), never bare magic numbers.
- Use forward slashes in every path.
- **How to discover what to bundle:** run the skill on three real tasks and read the transcripts.
  If every run independently wrote the same helper, promote it into `scripts/`.

## Assets

Short output templates can live inline in SKILL.md. Longer templates, or ones needed only in
certain branches, belong in `assets/` and get referenced so they load only when required.

## Frontmatter fields worth knowing

The open spec requires only `name` and `description` (plus optional `license`, `compatibility`,
`metadata`, `allowed-tools`). Harnesses add more; if you plan to distribute a skill outside one
harness, stick to the spec fields, because unknown keys can fail packaging outright.

| Field | Use |
|-------|-----|
| `disable-model-invocation: true` | Only the user invokes it (deploys, commits, sends) |
| `user-invocable: false` | Only the agent invokes it (background knowledge, legacy context) |
| `allowed-tools` | Pre-approves tools for the invoking turn; clears on the next user message |
| `disallowed-tools` | Removes tools while the skill is active |
| `paths` | Glob patterns limiting activation to matching files |

**Critical caveat:** `allowed-tools` grants permission without prompting; it does not remove other
tools from availability. Real restriction comes from permission rules. A security-audit skill
listing Write and Bash is a standing risk regardless of what the body says.

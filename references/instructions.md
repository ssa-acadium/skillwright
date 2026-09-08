# Instruction Calibration

The meta-rule: match instruction freedom to task fragility. Authors consistently err toward
over-constraining because rigid instructions feel safer. They are not safer; they just fail
differently.

## Control tuning

| Freedom | Form | When |
|---------|------|------|
| High | Prose, "use your judgement" | Open-field work with many valid approaches: code review, research |
| Medium | Pseudocode, parameterised scripts | Preferred-but-flexible flows: deploy runbooks |
| Low | Exact commands, "do not modify this" | Fragile operations: migrations, destructive edits |

For each step, ask how much variance is acceptable.

Optionally open with a persona to set the judgement rubric for the whole skill: "You are a senior
code reviewer focused on correctness over style."

## Explain the why

Strings of ALWAYS / NEVER / MUST give rules with no context. The agent follows the letter and
misses edge cases you did not anticipate. All-caps imperatives are a documented yellow flag.

```text
# Weak
MUST use constructor injection. NEVER use field injection.

# Strong
Use constructor injection. Field injection breaks testability because we cannot mock the
field without a Spring context.
```

The reasoning becomes the rubric for cases the skill never spelled out. Reserve bare imperatives
for genuinely fragile steps, since explanations cost tokens.

## Templates

When output structure matters (reports, commit messages, payloads), the shape is usually implicit
and each run rediscovers a slightly different one. Ship a template with placeholders.

Two strictness levels:

- **Strict** — "Use this exact template structure." For machine-parsed output or data contracts.
- **Flexible** — "A sensible default; adapt sections as needed." For documents where judgement
  helps. Prefer flexible unless a machine reads the output; strict templates suppress useful
  structural variation.

## In-skill examples

Templates show the skeleton; examples show populated instances. Embed 2-3 labelled pairs:

```text
Input: Fixed a bug where the export button double-submitted.
Output: fix(export): prevent duplicate submission on rapid clicks

Input: Added dark mode toggle to settings.
Output: feat(settings): add dark mode toggle
```

Use this wherever quality depends on style as much as structure: commit messages, release notes,
changelogs, review summaries. Beware drift: if your three examples share a subtle bias, the agent
reproduces that bias every time. Make examples span the variation the skill must support.

## Known gotchas

Skills documenting only the happy path teach what to do, not what to watch for. The first real edge
case and the agent invents a fix. Add a dedicated section listing concrete failure modes you have
actually observed:

```text
- Scanned PDFs return [] silently. Check page type before extracting.
- Rotated pages need page.rotation = 0 before column extraction.
- The --recursive flag works on GNU grep but not BSD grep.
```

This is often the most valuable content in a mature skill. It is also a moving target: libraries
update, and a stale gotcha sends the agent chasing a problem that no longer exists. Prune it.

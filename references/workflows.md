# Workflow Control

## Execution Checklist (linear multi-step)

Long procedures fail by skipping validation steps, losing track of position, or declaring done
prematurely at step 4 of 6. Provide a checklist the agent copies into its response and ticks off as
it works. Because it renders in the conversation, skipping a step becomes visible to both parties.

```markdown
## Progress
- [ ] 1. Validate input schema
- [ ] 2. Fetch source records
- [ ] 3. Transform
- [ ] 4. Write output
- [ ] 5. Verify row count matches source
```

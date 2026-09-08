# Descriptions and Triggering

The description field is the only signal an agent has before it decides to load a skill. An
under-specified description never fires; an over-broad one hijacks unrelated work.

## Writing rules

1. **Imperative framing.** "Use this skill when..." beats "This skill does...". The agent is
   deciding whether to act, so tell it when to act.
2. **Third person, specific, keyword-rich.** "Processes Excel files and generates reports", not
   "I can help you with spreadsheets".
3. **User intent, not implementation.** Describe what the user is trying to achieve. The agent
   matches against the user's words, not your internals.
4. **Be a little pushy.** Agents under-trigger. List contexts explicitly, including cases where
   the user does not name the domain: "even if they don't explicitly mention 'CSV' or 'analysis'".
5. **End with an exclusion clause.** "Do NOT use for blog articles, newsletters, or tweets."
   Positive triggers pull a skill in; exclusions push it out. Both are needed.
6. **Stay under the cap.** The spec limits `description` to 1024 characters. Some harnesses
   truncate the combined description plus `when_to_use` at 1536 characters in the skill listing.
   Every sentence competes for that budget.

## Before / after

### Before
description: Process CSV files.

### After
description: Analyze CSV and tabular data files — compute summary statistics, add derived columns,
  generate charts, and clean messy data. Use this skill when the user has a CSV, TSV, or
  Excel file and wants to explore, transform, or visualize the data, even if they don't
  explicitly mention "CSV" or "analysis". Do NOT use for database ETL, Excel formula
  authoring, or Google Sheets automation.

The improved version is more specific about *what* it does and broader about *when* it applies,
while carving out adjacent territory.

## Trigger evaluation

A description you have not tested is a guess. Build an eval set. Example:

```json
[
  {
    "query": "I've got a spreadsheet in ~/data/q4_results.xlsx with revenue in col C and
              expenses in col D — can you add a profit margin column and highlight anything
              under 10%?",
    "should_trigger": true
  },
  {
    "query": "can you write a python script that reads a csv and uploads each row to our
              postgres database",
    "should_trigger": false
  }
]
```

Aim for ~20 queries: 8-10 should-trigger, 8-10 should-not.

- **Vary should-trigger queries** along phrasing (formal, casual, typos), explicitness (names the
  domain vs. describes the need), detail, and complexity. The most useful ones are cases where the
  connection is not obvious from the query alone.
- **The most valuable negatives are near-misses** that share keywords but need something else.
  "Write a fibonacci function" tests nothing. "Update the formulas in my Excel budget" is a real test.
- **Include realism**: file paths, personal context ("my manager asked me to..."), column names,
  abbreviations.

## Measuring

Model behavior is nondeterministic. Run each query 3 times and compute a trigger rate (fraction of
runs where the skill loaded). A should-trigger query passes above ~0.5; a should-not-trigger query
passes below it.

## Avoiding overfitting

Split the query set ~60/40 into train and validation. Only train-set failures guide your edits;
validation tells you whether the edits generalize. Keep the proportional mix of positives and
negatives in both halves, and keep the split fixed across iterations.

- If should-trigger queries fail, the description is too narrow. Broaden scope or add contexts.
- If should-not-trigger queries fire, it is too broad. Add "does not do" clauses or clarify the
  boundary with adjacent capabilities.
- Do **not** paste keywords from failed queries into the description. Find the general category
  those queries represent and address that instead.
- If stuck after a few iterations, rewrite the description structurally rather than tweaking it.
- Five iterations is usually enough. Pick the best iteration by validation pass rate, which may not
  be the last one.

Finally, sanity-check with 5-10 fresh queries that were never part of optimization.

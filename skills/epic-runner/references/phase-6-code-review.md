# Phase 6: Code Review

Your job is to perform a thorough code review of the implementation, simulating a senior
developer reviewing a junior developer's pull request. The implementation has already
passed testing (Phase 5) and been committed — this review works on top of that commit.

## Prerequisites

Verify that:

- The current phase in the state file is `"code-review"`.
- A commit was created at the end of Phase 5 (the implementation commit exists on the branch).

Read these from the state file paths:

- The story document — for acceptance criteria, design decisions, and "Areas This Story Must Not Change."
- The implementation plan JSON — for the planned changes.
- The epic overview — for resolved design decisions.

## Step 1: Review the Implementation Commit

Run `git show --stat HEAD` to see the files changed in the most recent commit (the
implementation commit from Phase 5). Then run `git diff HEAD~1` to see the full diff.

If the diff is large, review file by file using `git diff HEAD~1 -- <file>`.

## Important: What This Review Is and Isn't

This review focuses on things that require judgment and context — not things a linter
or formatter can catch. Do NOT flag formatting, whitespace, indentation, import ordering,
or any stylistic issue that automated tooling handles. Spend your attention on design,
correctness, and reuse.

## Step 2: Holistic Review (All Changes Together)

Before looking at individual files, review the changeset as a whole. This catches
problems that are invisible when reviewing files in isolation.

### Placement

- Does each piece of new code live in the right module/layer/directory? Now that you
  can see the actual implementation (not just the plan), does the placement still make
  sense? Sometimes implementation reveals that a function belongs somewhere else.
- Are there hidden coupling or dependencies being created between components that
  should be independent?

### Completeness

- Are there files that were created but never imported or called by anything?
  (e.g., a new service with no consumer, a new component that isn't rendered anywhere)
- Are there files that were modified to call something that doesn't exist yet or
  wasn't created in this commit?
- Does the full set of changes form a complete, working feature end-to-end?

### What's Missing

- Are there error paths that should exist but don't? (e.g., what happens when a network
  call fails, a database query returns nothing, or a multi-step process fails halfway through)
- Are there edge cases the story document or acceptance criteria imply but the code
  doesn't handle? (e.g., empty states, loading states, permission boundaries)
- Are there null/undefined guards missing at boundaries where data enters the system?

### DRY — Duplication and Reuse

**This is a high-priority check.** Violations of DRY (Don't Repeat Yourself) are one of
the most common issues in generated code and compound quickly across stories.

- Do any of the new files duplicate each other's purpose? (e.g., two services that
  do essentially the same thing, two utility functions with overlapping logic)
- Is there logic in the new code that already exists elsewhere in the codebase?
  Search for existing utilities, helpers, and shared components before accepting new ones.
- Is business logic being recreated in a second place instead of reused from the first?
  (e.g., validation rules duplicated across a form and an API endpoint)
- Could any of the new code be consolidated without losing clarity?

If duplication is found, flag it as a must-fix. The fix is to extract shared logic
into a single reusable piece, not to leave both copies in place.

### Plan Adherence

- Does the implementation match what the plan described? No missing pieces, no extras.
- Were all sections of the plan addressed (data model, service, API, UI, routing, etc.)?
- If the developer deviated from the plan, is there a clear reason?

### Acceptance Criteria Traceability

Go through each acceptance criterion from the story document and identify the specific
code that implements it. Present as a traceability table:

```
AC Traceability:
  [AC text] -> implemented in: [file:function/component]
  [AC text] -> implemented in: [file:function/component]
  [AC text] -> NOT FOUND IN CODE
```

If any AC cannot be traced to code, flag it as a must-fix.

Beyond traceability, verify **correctness**: does the code actually implement the
criterion correctly? A function can exist for an AC and still get the logic wrong.

### Story Boundary Compliance

- Check "Areas This Story Must Not Change" from the story document. Verify no files or
  product surfaces listed there were modified.
- Check for scope creep — code that goes beyond what the plan specified.

### Design Decision Compliance

- Verify that the story's design decisions (hard constraints) were respected.
- Verify that relevant epic-level resolved design decisions were followed.

## Step 3: Per-File Review

Now walk through each changed file individually. Skip anything a linter or formatter
would catch — focus on substance.

- **Dead code**: Unused imports, unreachable branches, commented-out code, unused variables.
- **Naming consistency**: Do new names follow the patterns established in adjacent files?
  If the codebase uses `getUserById`, don't introduce `fetchUser`. Match the existing
  conventions for prefixes, suffixes, casing, and verb choices.
- **Duplication with existing code**: Does this file reimplement something that already
  exists in the codebase? Check utilities, helpers, and shared modules.
- **Error handling at boundaries**: Is error handling present where data enters or leaves
  the system (user input, API calls, external services, database queries)? For multi-step
  operations, what happens if one step fails partway through?
- **Hardcoded values**: Magic numbers, hardcoded strings that should be constants or config.
- **Bad precedents**: Will this pattern get copy-pasted into future stories? If the approach
  is questionable, flag it now before it compounds.

## Step 4: Findings Report

Categorize every finding:

```
Code Review Findings:

Must Fix:
  - [file:line] [description] — [why this must change]
  - [file:line] [description] — [why this must change]

Should Fix:
  - [file:line] [description] — [suggestion]
  - [file:line] [description] — [suggestion]

Notes:
  - [observation that needs no action]
```

**Must Fix** — Bugs, missing AC coverage, plan violations, boundary violations, broken
patterns that would fail a real PR review.

**Should Fix** — Style inconsistencies, minor improvements, naming suggestions. These
are recommendations, not blockers.

**Notes** — Observations, context for the user, things to be aware of in future stories.

If the review finds no issues at all, say so explicitly:
"Code review complete — no issues found. The implementation matches the plan and all
acceptance criteria are traceable to code."

## Step 5: Fix Cycle

If there are **Must Fix** items:

1. Apply each fix.
2. After all fixes, re-run `npm run lint` and `npm run build` (or the project's equivalent).
3. Re-review only the changed lines to confirm fixes are correct and don't introduce new issues.
4. Present the updated findings report showing resolved items.

If there are **Should Fix** items:

1. Present them to the user.
2. Ask: "These are suggestions, not blockers. Want me to apply any of them?"
3. Apply only the ones the user approves.

## Step 6: Approval Gate

Present the final review summary:

```
Code Review Summary:
  Files reviewed: [N]
  Must Fix: [N found, N resolved]
  Should Fix: [N found, N applied]
  AC Traceability: [N/N traced]
```

Ask: "Code review is complete. Ready to move to shipping, or do you want to discuss
any findings?"

## Step 7: Commit (If Changes Were Made)

If fixes were applied during this phase:

1. Run `git add` for only the files changed during code review.
2. Propose a commit message:

   ```
   fix(epic-N/story-N): address code review feedback
   ```

3. Ask the user to confirm or revise the message.
4. Commit with the approved message.

If no changes were made, skip this step entirely.

## Step 8: State File Update

After the user approves:

1. Set `current_phase` to `"shipping"`.
2. Update `updated_at`.
3. Tell the user: "Code review complete. Run `/epic-runner ship` to push and wrap up."

---
name: long-task-docs
description: Create or maintain docs/prompt.md, docs/implement.md, and docs/documentation.md for long-running software implementation tasks. Use when a repository needs consistent project requirements, autonomous implementation rules, and implementation-accurate documentation, or when those files have drifted and need to be aligned.
---

# Long Task Docs

Create or update the repository's long-task documents so implementation can continue consistently across sessions and agents.

Use the templates in `templates/` as the structural baseline. Preserve repository-specific facts and existing user-authored constraints when updating existing files.

This skill always creates or updates the following locations:

```text
docs/prompt.md
docs/implement.md
docs/documentation.md
docs/plans.md
```

Do not detect or adopt another document location, and do not place a temporary plan elsewhere.

## File responsibilities

Keep the long-task documents intentionally separate.

- `prompt.md`: Defines **what must be built** — goals, hard requirements, deliverables, product specification, quality requirements, and overall completion conditions.
- `implement.md`: Defines **how implementation work must proceed** — milestone execution, validation, bug handling, documentation updates, and completion rules.
- `documentation.md`: Defines **what currently exists and how to use it** — setup, commands, repository structure, data formats, implemented feature behavior, limitations, and troubleshooting.
- `docs/plans.md`: Defines **local work planning state** — milestones, decisions, verification results, and residual risks for the documentation-maintenance work. It is not a user-facing specification or the authoritative permanent project history.

Do not duplicate local planning state across the three tracked documents. Keep it only in `docs/plans.md`.


## Inputs to inspect

Before writing, inspect the repository as far as available:

- User request and latest project requirements
- Existing `docs/prompt.md`, `docs/implement.md`, `docs/documentation.md`, and `docs/plans.md`
- Repository instructions such as `AGENTS.md`
- `README.md` and other architecture or design documents
- Project structure and main modules
- Package/build configuration and runnable scripts
- Test configuration and existing verification commands
- Current Git diff when updating an existing repository

Prefer repository evidence over assumptions. Do not invent commands, supported environments, dependencies, endpoints, or implemented behavior.

## Workflow

### 1. Determine the operation

Classify the task as one of:

- **Create**: one or more files do not exist.
- **Refresh**: files exist but are stale or incomplete.
- **Align**: files disagree with each other or with the repository.

When updating existing files, retain valid project-specific content instead of replacing it wholesale with the templates. Use `docs/plans.md` as the local plan for this maintenance work.

### 2. Establish project facts

Extract the minimum facts needed to populate the files:

- Project name and purpose
- Target users or consumers
- Runtime and supported environment
- Main technology stack
- Required external services and restrictions
- Primary features and workflows
- Persistence and integration boundaries
- Build, lint, typecheck, test, package/export commands
- Repository structure and core modules
- Important quality attributes such as determinism, compatibility, performance, security, or offline operation

If a value cannot be verified, keep the wording explicit about uncertainty or use a narrow placeholder rather than inventing a fact.

### 3. Create or update `prompt.md`

Start from `templates/prompt.md`.

Keep `prompt.md` focused on the desired product and non-negotiable constraints.

Include:

- Core goals
- Hard requirements
- Deliverables
- Product specification
- Quality requirements
- High-level process requirements
- Completion conditions

Avoid:

- Detailed step-by-step implementation procedure
- Per-milestone status
- Long decision logs
- Current feature documentation
- Duplicating `implement.md`

Instruct the implementation agent to create or maintain `docs/plans.md` before substantial documentation work. It records local milestones, decisions, verification results, and residual risks only.

### 4. Create or update `implement.md`

Start from `templates/implement.md`.

Keep `implement.md` stable and reusable across the project lifecycle. It should change much less often than the local `docs/plans.md`.

Include:

- Non-negotiable execution constraints
- Milestone execution rules
- Validation requirements
- Bug handling expectations
- Documentation synchronization rules
- Completion criteria

Prefer explicit operational rules such as:

- Use `docs/plans.md` as the local work plan for this documentation-maintenance task.
- Work on one milestone at a time.
- Validate before advancing.
- Do not hide failures by weakening tests.
- Preserve unrelated user changes.
- Record only decisions that affect future work.

Avoid project feature specifications that belong in `prompt.md` and local planning state that belongs in `docs/plans.md`.


### 5. Create or update `documentation.md`

Start from `templates/documentation.md`.

Document only behavior that is implemented and currently usable.

Include:

- What the project is
- Current status and known limitations
- Local setup
- Verified development and quality commands
- Demo or common workflows
- Repository structure overview
- Data/file/API format overview when relevant
- Feature reference with implementation locations
- Troubleshooting

Do not describe planned features as completed. If documentation cannot be verified from code, tests, or a runnable command, state the limitation rather than guessing.

### 6. Move the local plan to ignored management

At the final stage of documentation maintenance, add exactly `docs/plans.md` to `.gitignore`. Do not ignore `docs/prompt.md`, `docs/implement.md`, or `docs/documentation.md`.

If `docs/plans.md` is already Git-tracked, keep the physical file and run:

```sh
git rm --cached -- docs/plans.md
```

Then verify the ignore rule with `git check-ignore -v docs/plans.md`. Do not delete the local plan as part of this transition.

### 7. Cross-check the documents

Before completion, verify:

- `docs/prompt.md` requirements are not contradicted by `docs/implement.md`.
- `docs/implement.md` points to `docs/plans.md` and `docs/documentation.md`.
- `docs/documentation.md` contains only implemented facts.
- Commands in documentation exist in repository configuration.
- Environment/runtime versions are consistent.
- Terminology and project naming are consistent.
- Important constraints from the user are not lost.
- The same large block of content is not duplicated between files.
- `.gitignore` targets only `docs/plans.md`, and no tracked documentation file is accidentally excluded.

### 8. Preserve scope and user changes

When modifying an existing repository:

- Do not delete unrelated content merely because it is absent from the templates.
- Do not overwrite user-authored constraints without evidence that they are obsolete.
- Avoid unrelated code changes.
- Keep diffs reviewable.
- If the repository already has a different documentation convention, adapt the templates to that convention while preserving the three file responsibilities.

## Output requirements

Create or update:

```text
docs/prompt.md
docs/implement.md
docs/documentation.md
docs/plans.md
```

When the user asks for templates rather than project-specific files, return or copy the files under this skill's `templates/` directory without filling project placeholders.

Keep `docs/prompt.md`, `docs/implement.md`, and `docs/documentation.md` tracked. At the final stage, make `docs/plans.md` local-only through the `.gitignore` and index-transition procedure above. When actual repository files are produced, fill all values that can be verified. Leave placeholders only for genuinely unresolved inputs.

## Final checks

Before reporting completion:

- Confirm all four requested documents exist under `docs/`.
- Confirm Markdown structure is valid and concise.
- Confirm there are no fabricated commands or features.
- Confirm `prompt.md`, `implement.md`, and `documentation.md` have distinct responsibilities.
- Confirm references to `docs/plans.md`, repository paths, and commands are valid where verifiable.
- Confirm `docs/plans.md` is ignored after the final transition, while the other three documents remain tracked.
- Summarize what was created or updated and identify any unresolved placeholders or unverifiable facts.

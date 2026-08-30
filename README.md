# Spec Kit + Superpowers Workflow

A spec-driven development workflow that integrates
[GitHub Spec Kit](https://github.com/github/spec-kit) with
[Superpowers](https://github.com/obra/superpowers).

This workflow ensures that every implementation follows:

- An isolated Git worktree.
- Test-Driven Development (TDD).
- Incremental task execution.
- Verification after each task or phase.
- Code review before completion.
- Clean Git branch management.
- Optional commits after completing a task or phase.

## Purpose

This project combines:

- **Spec Kit** for requirements, specifications, technical planning,
  and task breakdown.
- **Superpowers** for disciplined implementation, TDD, isolated worktrees,
  code review, verification, and branch completion.

The AI coding agent must not immediately start writing production code.
It must first understand the requirements, create or validate a plan,
and then implement the plan incrementally.

## Prerequisites

Install the required tools before starting:

- Git
- Python and `uv`
- The selected AI coding agent
- Superpowers installed and enabled for the selected agent

Install or run Specify with:

```bash
uv tool install specify-cli --from git+https://github.com/github/spec-kit.git
```

Verify the installation:

```bash
specify --help
specify check
```

## Initialize a Project

Create a new project with Spec Kit:

```bash
specify init {project_name} \
  --integration {AI_coding_agent} \
  --force
```

Example:

```bash
specify init my-project \
  --integration copilot \
  --force
```

Then enter the project directory:

```bash
cd {project_name}
```

The `--force` option allows Spec Kit to initialize or overwrite the
generated integration files when required.

## Update AGENTS.md / CLAUDE.md

```text
# Repository agent rules

## Source priority
1. User instructions and the current task.
2. Current repository code, configs, tests, lockfiles, and local documentation.
3. Official web documentation, official release notes, and other primary sources found via web search.
4. Reputable secondary web sources used only to supplement or cross-check primary sources.
5. Training knowledge for background only, never as the final source of truth for current or project-specific facts.

## Retrieval-first rules
- If local evidence is insufficient, search the web before answering or coding.
- For anything version-sensitive, time-sensitive, external, or likely to have changed, do not rely on memory alone.
- Prefer official vendor documentation over blogs, forum posts, or summaries.
- If the task depends on current package behavior, framework conventions, APIs, pricing, policies, release notes, or platform behavior, verify with web sources before making changes.
- EXPLICIT TRIGGER: Requests for "templates", "boilerplates", or setup files are ALWAYS version-sensitive. You MUST run tools (`search_web` or terminal commands) to fetch current latest versions and syntaxes before generating them. NEVER generate these from memory.

## No memory-first behavior
- Do not use training knowledge as the first fallback when local evidence is missing.
- Do not present memory-based factual claims as confirmed facts when they have not been verified.
- Use training knowledge only to understand the problem, form better search queries, and assess plausibility.

## Conflict handling
- If web evidence conflicts with memory, prefer the web evidence.
- If multiple web sources conflict, prefer the most official and most recent source, and mention the conflict.
- If no reliable source can be found, state uncertainty explicitly instead of guessing.

## Coding rules
- Read existing code and nearby tests before editing.
- Do not guess API signatures, config formats, framework conventions, CLI flags, dependencies, version numbers, or migration steps from memory.
- Keep changes aligned with existing repository patterns unless the task requires otherwise.
- Make the smallest change that solves the task.

## Validation
- Run the smallest relevant validation first, then broader checks if needed.
- Prefer targeted tests, lint, and typecheck for the affected area.
- If validation cannot be run, explain what should be run and why.

## Boundaries
- Never invent facts, versions, commands, or URLs.
- Ask before destructive actions, schema changes, secret handling, or irreversible migrations.
- If evidence is weak, incomplete, or conflicting, say so clearly.
```

## Establish the Constitution

Start the AI coding agent in the project directory and run:

```text
/speckit.constitution
```

Use the following project constitution:

```text
Task Generation Standards

The `speckit-tasks` agent skill MUST follow rigorous planning and phase-level
decomposition standards when generating `tasks.md`. Every phase generated in
`tasks.md` MUST explicitly list and incorporate the following requirements:

1. **Phase 1 Worktree Creation**:
   - Phase 1 MUST prioritize creating a new git worktree for workspace isolation
     before starting implementation tasks.
   - It MUST ask the user to confirm the creation of the new worktree, defaulting
     to creating a new one.
2. **Dedicated Subagent Execution per Phase**:
   - Each phase MUST be executed within a dedicated subagent session to maintain
     clean context boundaries and isolated task execution.
3. **Mandatory Test-Driven Development (TDD)**:
   - Implementation tasks within each phase MUST strictly follow TDD
     (Red-Green-Refactor): write a failing test first, verify failure, implement
     minimal code to make it pass, and refactor while maintaining green tests.
4. **Iterative Review & Bug Hunt Subagent Loop**:
   - At the end of each phase, a dedicated subagent MUST be spawned to conduct
     thorough code review, verify eslint, spec compliance verification, and bug hunting.
   - If any bugs or discrepancies are found, they MUST be resolved immediately.
   - After resolving identified issues, another review subagent MUST be spawned
     to re-evaluate and hunt for remaining bugs.
   - This cycle (Review Subagent → Fix Bugs → Re-review Subagent) MUST repeat
     iteratively until zero bugs remain.
5. **Phase-End Commit**:
   - Once all tasks in the phase are verified and the review loop confirms zero
     bugs, all phase changes MUST be committed with a descriptive conventional
     commit message.
6. **Final Feature-Level Review Phase**:
   - The final phase in `tasks.md` MUST be dedicated entirely to a holistic,
     feature-level review encompassing all previous phases.
   - A subagent MUST be spawned to conduct a comprehensive bug hunt and
     integration review across the entire implemented feature.
   - Any bugs found MUST be fixed, followed by another review subagent execution,
     repeating this cycle until zero bugs remain across the entire feature.
   - Once the final review loop confirms zero bugs, a final comprehensive commit
     MUST be made to finalize the feature implementation.
7. **Task Specification Quality**:
   - Every task MUST specify exact file paths for all files to be created or
     modified (vague references are prohibited).
   - Every task MUST include complete code or detailed pseudocode, not high-level
     summaries.
   - Every task MUST include explicit verification steps (exact test commands,
     expected outputs, or acceptance criteria).
   - Tasks MUST be ordered by dependency; referencing unbuilt upstream
     dependencies without declaring them first is prohibited.
   - The spec (`spec.md`) and plan (`plan.md`) MUST both be read before
     generating tasks; partial context generation is prohibited.
   - All phase workflow requirements (worktree creation in Phase 1, subagent
     execution, TDD steps, iterative review subagent loop, phase-end commit,
     and the final feature-level review phase) MUST be explicitly listed as
     actionable checklist items in `tasks.md`.
```

## Recommended Development Flow

Use the following sequence for each feature:

```text
brainstorming
→ speckit.constitution(if needed)
→ speckit.specify
→ speckit.clarify
→ speckit.plan
→ speckit.tasks
→ speckit-implement
```

Spec Kit provides the specification and planning stages, while Superpowers
provides the implementation discipline and quality gates [web:8][web:11].

## Standard Commands

### Define the feature

```text
/speckit.specify
```

Describe what should be built and why. Focus on behavior, requirements,
acceptance criteria, and user value.

### Clarify requirements

```text
/speckit.clarify
```

Resolve ambiguity before creating the technical implementation plan.

### Create the technical plan

```text
/speckit.plan
```

Define the architecture, technology choices, data model, APIs, testing strategy,
and implementation constraints.

### Create implementation tasks

```text
/speckit.tasks
```

Break the plan into small, ordered, independently verifiable tasks.

### Analyze the plan

```text
/speckit.analyze
```

Check consistency between the specification, plan, and task list before coding.

### Implement the tasks

```text
/speckit.implement
```

Implementation must follow the Superpowers rules in this document:

1. Work inside an isolated worktree.
2. Work on the dedicated feature branch.
3. Execute one task at a time.
4. Apply TDD.
5. Verify each task.
6. Review the implementation.
7. Commit after a verified task or phase.

## Task Execution Protocol

For every task, follow this protocol:

### 1. Prepare

- Read the specification.
- Read the technical plan.
- Read the task details.
- Check the current branch and worktree.
- Confirm the baseline is clean.

### 2. Write the test

Create or update the test that describes the expected behavior.

The test must fail before the implementation is added.

### 3. Implement

Write the smallest implementation that makes the test pass.

Avoid unrelated refactoring or scope expansion.

### 4. Verify

Run the narrowest relevant test first, then run broader checks:

```bash
npm test -- <test-file>
npm run lint
npm run typecheck
```

Use equivalent commands for the project's technology stack.

### 5. Review

Inspect the changes:

```bash
git status
git diff
```

Check the implementation against:

- The specification.
- The acceptance criteria.
- The technical plan.
- The project constitution.

### 6. Update the task

Mark the task as completed only after all verification checks pass.

### 7. Commit

If the task is independently complete, create a focused commit:

```bash
git add <intended-files>
git commit -m "feat: complete <task-name>"
```

If the task belongs to a tightly coupled phase, wait until the phase is complete:

```bash
git add <intended-files>
git commit -m "feat: complete <phase-name>"
```

## Commit Rules

Allowed commit points:

- After a verified independent task.
- After a verified group of tightly coupled tasks.
- After a fully verified phase.

Required commit format:

```text
<type>: <short description>
```

Recommended types:

- `feat`: New functionality.
- `fix`: Bug fix.
- `test`: Test-only changes.
- `refactor`: Internal restructuring.
- `docs`: Documentation changes.
- `chore`: Maintenance changes.
- `perf`: Performance improvement.
- `security`: Security-related changes.

Examples:

```bash
git commit -m "feat: add session validation"
git commit -m "test: cover expired session behavior"
git commit -m "fix: handle invalid authentication tokens"
git commit -m "feat: complete checkout validation phase"
```

## Prohibited Behavior

The AI coding agent must not:

- Work directly on the main branch for isolated feature work.
- Skip the failing-test step in TDD.
- Implement multiple unrelated tasks at once.
- Mark tasks complete without verification.
- Claim tests pass without running them.
- Ignore code review findings.
- Commit failing or incomplete code.
- Commit unrelated files.
- Rewrite history unless explicitly requested.
- Use destructive Git commands without confirmation.
- Remove a worktree before the branch is integrated or explicitly discarded.
- Expand the scope beyond the approved specification.

## Completion Checklist

Before declaring a task complete:

- [ ] The task is defined in `tasks.md`.
- [ ] The implementation is inside the correct isolated worktree.
- [ ] A failing test was written first.
- [ ] The implementation makes the test pass.
- [ ] Relevant tests pass.
- [ ] Lint passes.
- [ ] Type checking passes, if available.
- [ ] The diff was reviewed.
- [ ] No unrelated files were changed.
- [ ] The task status was updated.
- [ ] A focused commit was created when appropriate.

Before declaring a phase complete:

- [ ] All phase tasks are complete.
- [ ] All phase tests pass.
- [ ] The implementation was reviewed.
- [ ] The full relevant verification suite passes.
- [ ] The final diff was reviewed.
- [ ] The phase was committed.
- [ ] Remaining limitations are documented.

## Example End-to-End Session

```bash
specify init my-project \
  --integration copilot \
  --force

cd my-project
```

Run the following commands in the AI coding agent:

```text
/speckit.constitution
/speckit.specify Build a secure user authentication module
/speckit.clarify
/speckit.plan
/speckit.tasks
/speckit.analyze
/speckit.implement
```

During implementation, the agent must:

```text
1. Create an isolated worktree.
2. Create a feature branch.
3. Verify the clean test baseline.
4. Select the next task.
5. Write a failing test.
6. Implement the minimum solution.
7. Run the tests and quality checks.
8. Review the diff.
9. Mark the task complete.
10. Commit the task or wait for the phase commit.
11. Continue with the next task.
```

After all tasks are complete:

```bash
git status
git diff
npm test
npm run lint
npm run typecheck
npm run build
```

Then the agent must finish the branch cleanly by offering the appropriate
integration or cleanup option.

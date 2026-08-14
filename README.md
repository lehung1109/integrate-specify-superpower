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

## Establish the Constitution

Start the AI coding agent in the project directory and run:

```text
/speckit.constitution
```

Use the following project constitution:

```text
Create and maintain the project constitution with these mandatory principles:

1. Spec-driven development

   Every implementation must be based on an approved specification,
   technical plan, and task list. Do not implement undefined requirements.

2. Superpowers workflow is mandatory

   All implementation tasks must follow the Superpowers workflow:

   - Use an isolated Git worktree.
   - Create or validate an implementation plan.
   - Apply Test-Driven Development.
   - Execute tasks incrementally.
   - Verify each task before continuing.
   - Review the implementation against the specification and plan.
   - Run the appropriate tests, type checks, linters, and build commands.
   - Finish the branch cleanly.

3. Isolated worktree

   Never implement feature work directly on the main branch or the user's
   current working directory when an isolated worktree is required.

   Before implementation:

   - Create a dedicated worktree.
   - Create or use a dedicated feature branch.
   - Verify the worktree has a clean baseline.
   - Run the existing test suite before changing the code.

4. Test-Driven Development

   For every behavior change:

   - Write a failing test first.
   - Run the test and confirm that it fails for the expected reason.
   - Implement the minimum production code required.
   - Run the test and confirm that it passes.
   - Refactor only after the tests pass.
   - Run the relevant test suite again.

5. Incremental implementation

   Execute one task at a time. Do not silently skip, combine, or reorder tasks
   unless the plan is updated and the change is explicitly explained.

   After each task:

   - Review the changed files.
   - Run task-specific verification.
   - Confirm that no unrelated behavior was broken.
   - Update the task status.

6. Phase completion

   A phase is complete only when all tasks in that phase are implemented,
   tested, reviewed, and verified.

   The agent must report:

   - Completed tasks.
   - Tests and verification commands executed.
   - Files changed.
   - Known limitations.
   - Recommended next phase.

7. Commit policy

   The agent is allowed to create a Git commit after either:

   - A task is completed and verified; or
   - A phase is completed and verified.

   A commit must not be created when:

   - Tests are failing.
   - Type checking is failing.
   - Linting has unresolved errors.
   - The implementation does not satisfy the specification.
   - The worktree contains unrelated changes.
   - The agent has not reviewed the diff.

   Before committing:

   - Run the relevant verification commands.
   - Review `git diff`.
   - Review `git status`.
   - Confirm that only intended files are staged.
   - Use a clear Conventional Commit message.

   Example:

   ```bash
   git status
   git diff
   git add <intended-files>
   git commit -m "feat: implement user authentication task"
   ```

   The agent must never use `git add .` or `git commit -a` blindly.

8. Commit granularity

   Prefer one focused commit per completed task when the task is logically
   independent.

   Use one phase-level commit when:

   - Tasks are tightly coupled.
   - Intermediate states are not independently useful.
   - The phase is the smallest safe unit of delivery.

9. Verification before completion

   Never claim that a task, phase, or feature is complete without fresh
   verification evidence.

   Depending on the project, run:

   ```bash
   npm test
   npm run lint
   npm run typecheck
   npm run build
   ```

   Use the project's actual scripts when they differ from these examples.

10. Code review

    Request or perform a code review after each significant task or phase.

    The review must check:

    - Specification compliance.
    - Plan compliance.
    - Test quality and coverage.
    - Security concerns.
    - Error handling.
    - Performance implications.
    - Maintainability.
    - Unintended changes.

    Critical review findings must be fixed before the task or phase is marked
    complete.

11. Clean branch completion

    When all tasks are complete:

    - Run the full verification suite.
    - Confirm the worktree status.
    - Review the final diff.
    - Ensure all intended changes are committed.
    - Present merge, pull request, or cleanup options.
    - Remove the worktree only after the branch has been integrated or
      explicitly discarded.

12. No unsupported completion claims

    Do not claim that code works, tests pass, or a task is complete unless the
    corresponding command has been executed successfully in the current state.
```

## Recommended Development Flow

Use the following sequence for each feature:

```text
brainstorming
→ using-git-worktrees
→ speckit.constitution
→ speckit.specify
→ speckit.clarify
→ speckit.plan
→ speckit.tasks
→ speckit.analyze
→ executing-plans
→ test-driven-development
→ code-review
→ verification
→ commit
→ finishing-a-development-branch
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

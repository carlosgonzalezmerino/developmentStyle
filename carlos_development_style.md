# Development style

Carlos software development style

Prefer object-oriented design where it clarifies responsibilities; see [design principles, preferred patterns, and practices to avoid](design_patterns.md) for guidance.

Section headings are **stable**: they are used to refer to a specific agreement in a PR or
when talking to the AI ("complies with the *Testing* agreement").

---

## Code Quality

- **Readability comes first.** Write code that another developer can understand without reconstructing hidden control flow. Favor clear, straightforward solutions over clever or overly abstract ones.
- Methods and functions must not exceed **30 lines** (excluding blank lines and docstrings).
  Refactor into smaller helpers if needed, while preserving a clear flow of execution.
- Each method has a **single responsibility**.
- **Do not overuse private methods.** Extract one when it names a meaningful operation, isolates a distinct responsibility, or removes substantial duplication; avoid one-line wrappers and chains of helpers that make the main flow harder to follow.
- Use **descriptive, meaningful names**. No abbreviations unless universally understood
  (`id`, `url`, `http`).
- Naming conventions by language:
  - Python: `snake_case` for functions/variables, `PascalCase` for classes.
  - Scala/Java: `camelCase` for methods/variables, `PascalCase` for classes.
  - SQL: `snake_case` for columns/tables, `UPPER_CASE` for keywords.
- **No magic numbers.** Use named constants or configuration values.
- **Never hardcode secrets or credentials.** Use environment variables or secret managers.

## Software Definition and Construction

- Use **Specification-Driven Development (SpecsDD)**: define expected behavior, boundaries, acceptance criteria, and tests in a spec before implementation. Review and update the spec when the design changes; do not treat generated code as the source of truth.
- Use **Observability-Driven Development (ODD)**: define how the behavior will be observed alongside the spec. Identify relevant logs, metrics, traces, failure signals, and how to verify them in development; implement only the signals needed to diagnose and validate the behavior, without exposing secrets or personal data.
- Each implementation spec must show the proposed classes, their responsibilities, and their public methods (name, inputs, outputs, and relevant errors). Show important private methods only when they clarify the design; the spec is not a mandate to create unnecessary classes or helpers.
- Include a **sequence diagram** in the spec that shows how those classes interact for the main flow and at least the relevant failure flow. Label calls and responses, including where observability signals are emitted. Prefer a text-based diagram kept with the spec so it can be reviewed and updated alongside the code.
- Implement against the agreed spec, test the documented behavior and failure cases, and confirm that the planned signals make the running software diagnosable. Update the spec and diagram if the actual design changes.

## Documentation

- Every **public** method, function and class has an updated docstring with:
  - Brief description of what it does.
  - `Args`/`Parameters`: description and type of each parameter.
  - `Returns`: description and type of the return value.
  - `Raises` (if applicable): exceptions that can be raised.
- Inline comments only when the code is not self-explanatory. They explain the **why**, not
  the **what**.
- Never leave commented-out code (ghost code).

## Testing

- Every public method has at least one unit test.
- Tests cover:
  - Happy path (expected inputs/outputs).
  - Edge cases (boundary values, empty inputs, nulls).
  - Error cases (expected exceptions).
- Naming pattern: `test_<method>_<scenario>_<expected_result>`.
- Tests are **independent and idempotent**: no shared mutable state, no order dependency.
- Target coverage: **80% minimum**.

## Error Handling

- Never silently swallow exceptions. Always log or re-raise with context.
- Use **specific exception types**; no generic `except Exception`.
- Provide meaningful error messages that help with debugging.

## Git

- Commit message format: `<type>: <short description>`.
- Allowed types: `feat`, `fix`, `refactor`, `docs`, `test`, `chore`, `ci`.
- Small, atomic commits: one logical change per commit.
- Branch naming: `feature/`, `bugfix/`, `hotfix/` prefixes with descriptive names.

## Security

- Never include secrets, API keys, tokens or PII in generated code.
- Validate and sanitize all external inputs.
- Use parameterized queries; no string concatenation for SQL.

# Repository Selection

## Selected Repository

- Upstream repository: `https://github.com/stleary/JSON-java`
- Local fork remote: `https://github.com/Fahmida-Hossain-Charu/JSON-java.git`
- Primary language: Java

## Requirement Assessment

| Assignment requirement | Assessment | Evidence |
|---|---|---|
| Public GitHub repository | Meets requirement | The upstream GitHub URL and fork/upstream workflow are recorded in the local repository and project evidence |
| Written in Java | Meets requirement | Production implementation is under `src/main/java/org/json`; 26 production Java files were counted |
| Actively maintained | Meets requirement | Local Git history contains 1,475 commits and recent project activity; a GitHub activity screenshot/export should still be added for the final report |
| At least 500 GitHub stars | Evidence to be added | Add a dated screenshot or exported GitHub repository page showing the current star count |
| At least 100 commits | Meets requirement | Local Git history contains 1,475 commits |
| Manageable size: 2,000-50,000 LOC | Meets requirement | Local count: approximately 10,525 production Java LOC and 14,233 test Java LOC |
| Build instructions and local build/test support | Meets requirement | Maven and Gradle build files exist; `test-before-refactor-log.txt` records a successful Maven run |
| No heavy enterprise framework | Meets requirement | The architecture is a compact library centered on the `org.json` package |
| Suitable for code-smell analysis | Meets requirement | PMD evidence records hundreds of Design-ruleset findings, including complexity, God Class, excessive public API, nesting, and exception-handling concerns |

## Build and Test Evidence

The pre-refactor Maven test evidence records:

- 777 tests run.
- 0 failures.
- 0 errors.
- 6 skipped.
- `BUILD SUCCESS`.

This provides a strong baseline for later behavior-preserving refactoring and
functional validation.

## Why the Repository Is Suitable

JSON-java is a useful project for the assignment because it is a real-world,
widely used Java library with a straightforward package structure and strong
automated tests. The codebase is large enough to contain meaningful
maintainability issues while still allowing a narrow refactoring to be studied
in isolation.

The PMD evidence identifies concrete design smells such as:

- Cognitive and cyclomatic complexity.
- Deeply nested conditionals.
- God Class.
- Excessive public method count.
- Too Many Methods.
- Generic exception handling.

The `CDL` class offers a manageable area for a focused, behavior-preserving
refactoring. Its `rowToString(JSONArray, char)` method is small enough to study
carefully while still having measurable complexity and nesting findings.

## Selection Risk

The main risk is upstream Pull Request acceptance. JSON-java is a mature
compatibility-sensitive library, so broad architectural refactors or public API
changes may be rejected even when a static-analysis tool reports a smell.

The project should therefore:

- Keep the selected change narrow.
- Preserve behavior and public APIs.
- Add or rely on focused tests.
- Avoid large class splitting and mass PMD cleanup.
- Clearly explain why the change improves readability without changing output.

This risk does not make the repository unsuitable, but it strongly affects the
choice of refactoring target.

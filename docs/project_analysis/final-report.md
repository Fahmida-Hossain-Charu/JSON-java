# Final Report: Code Smells in the Wild

## 1. Introduction

This report documents the code-smell analysis and refactoring work completed
for the `stleary/JSON-java` repository. The project focused on identifying a
real maintainability issue, selecting a small and safe refactoring target, and
validating that behavior remained unchanged after the refactoring.

The selected refactoring target was:

- Repository: `stleary/JSON-java`
- Fork branch: `refactor/code-smell-fixes`
- File: `src/main/java/org/json/CDL.java`
- Method: `CDL.rowToString(JSONArray, char)`

## 2. Repository Selection Justification

JSON-java was selected because it is a real-world Java library with a compact
structure, a meaningful test suite, and enough code to support code-smell
analysis. The repository contains production code under `src/main/java/org/json`
and tests under `src/test/java/org/json/junit`.

The project evidence records:

- Public upstream repository: `https://github.com/stleary/JSON-java`
- Local fork remote: `https://github.com/Fahmida-Hossain-Charu/JSON-java.git`
- Primary language: Java
- Local history: 1,475 commits
- Approximate size: 10,525 production Java LOC and 14,233 test Java LOC

The repository was suitable because it is large enough to contain meaningful
design smells but small enough to support a focused, reviewable refactoring.
The main selection risk was upstream acceptance because JSON-java is a mature
compatibility-sensitive library.

## 3. Repository Setup and Build Process

The repository supports both Maven and Gradle. For this project, validation was
recorded with Maven:

```text
mvn test
```

The baseline Maven test run before refactoring is saved in
`test-before-refactor-log.txt` and `test-before-refactor.png`. The post-refactor
Maven test run is saved in `test-after-refactor-log.txt` and
`test-after-refactor.png`.

## 4. Architecture Overview

JSON-java is a reusable library rather than a standalone application. Client
code enters through public APIs such as `JSONObject`, `JSONArray`, `XML`, and
`CDL`.

Main components include:

| Component | Responsibility |
|---|---|
| `JSONObject` | Map-like JSON object representation, parsing, lookup, conversion, and serialization |
| `JSONArray` | Ordered JSON array representation, parsing, lookup, conversion, and serialization |
| `JSONTokener` | Character-level tokenizer used by JSON parsing logic |
| `XML` | Conversion between XML text and JSON model objects |
| `CDL` | Conversion between comma-delimited text and JSON arrays or objects |
| `JSONWriter` / `JSONStringer` | Streaming and string-based JSON writing |
| `JSONPointer` | Navigation through `JSONObject` and `JSONArray` values |

The selected refactoring was in `CDL`, a smaller conversion utility class,
rather than in the more central and public API-heavy `JSONObject` or
`JSONArray` classes.

## 5. Package/Module Diagram

All production classes are in the main `org.json` package.

```text
Client code
    |
    +--> JSONObject / JSONArray
    |       |
    |       +--> JSONTokener
    |       +--> nested JSON values
    |
    +--> XML / CDL / HTTP / Cookie
    |       |
    |       +--> adapters between external text formats and JSON model objects
    |
    +--> JSONWriter / JSONStringer
    |       |
    |       +--> serialized JSON text
    |
    +--> JSONPointer
            |
            +--> navigation through JSONObject / JSONArray
```

This project used a text diagram because the existing architecture evidence is
stored in Markdown.

## 6. Manual Code Quality Analysis

Manual inspection identified several maintainability concerns, including large
central classes, broad public APIs, repeated conversion responsibilities, and
exception-handling concerns. The primary manual finding was
`CDL.rowToString(JSONArray, char)`.

Manual smells for the selected method included:

- Complex conditional logic.
- Poorly structured method.
- Mixed responsibilities.
- Nested conditional logic.

The method handled delimiter insertion, null handling, value conversion,
quote-decision logic, character filtering, quote writing, and newline
termination in one method.

## 7. Tool-Based PMD Analysis

PMD was used for tool-based analysis with the Java Design ruleset. The saved
text report records PMD 7.21.0 and 475 findings across 85 Java files. The
IntelliJ screenshot records 470 findings from a separate run/settings context.

For `CDL.rowToString(JSONArray, char)`, PMD reported:

- `CognitiveComplexity`: 21, above threshold 15.
- `CyclomaticComplexity`: 13.
- `AvoidDeeplyNestedIfStmts` near line 195.

Other broad findings, such as God Class and Excessive Public Count in
`JSONObject` and `JSONArray`, were documented but not selected because they
would require larger and riskier public API changes.

## 8. Selected Smells for Refactoring

The primary selected smell was the complex and deeply nested implementation of
`CDL.rowToString(JSONArray, char)` in `src/main/java/org/json/CDL.java`.

This target was selected because:

- PMD and manual analysis both identified the same method.
- The method was small and localized.
- The change could preserve the public API.
- It was safer than restructuring `JSONObject` or `JSONArray`.
- It was safer than changing public API exception behavior.
- Behavior could be validated with existing tests.

`CDL.getValue(JSONTokener, char)` was documented as a secondary candidate, but
it was riskier because it affects parsing behavior.

## 9. Refactoring Approach and Reasoning

The refactoring used:

- Extract Method.
- Reduced Nested Conditional.

The implementation added private static helper methods:

- `appendRowValue(...)`
- `shouldQuoteValue(...)`
- `appendQuotedValue(...)`

The goal was to separate row-level coordination from value-level formatting.
After the refactoring, `rowToString` remains responsible for iterating through
the `JSONArray`, placing delimiters, and appending the final newline. The helper
methods make null handling, quote decisions, and quoted-value filtering easier
to understand.

The public method signature was preserved, and no public API behavior was
intentionally changed.

## 10. Before vs After Code Comparison

Before refactoring, `rowToString` contained all row construction and value
formatting logic in one nested block. The method decided whether to append a
delimiter, checked for null values, converted objects to strings, decided
whether quoting was required, filtered quoted characters, and wrote quotes.

After refactoring, the method delegates value formatting:

| Concern | Before | After |
|---|---|---|
| Row construction | Mixed with value-formatting details | Remains in `rowToString` |
| Null handling | Nested inside the loop | Handled by `appendRowValue` |
| Quote decision | Inline complex condition | Moved to `shouldQuoteValue` |
| Quoted-value filtering | Nested character loop | Moved to `appendQuotedValue` |
| Public API | Existing public method | Unchanged |

The logic was moved, not changed. The original quote decision and character
filtering conditions were preserved, delimiter placement remained between array
positions, null values still append no value text, and the final newline is
still appended at the end.

## 11. Functional Validation Evidence

The same command was used before and after the refactoring:

```text
mvn test
```

| Validation point | Tests run | Failures | Errors | Skipped | Result |
|---|---:|---:|---:|---:|---|
| Before refactoring | 777 | 0 | 0 | 6 | `BUILD SUCCESS` |
| After refactoring | 777 | 0 | 0 | 6 | `BUILD SUCCESS` |

The `CDLTest` portion of both logs reports 21 tests, 0 failures, 0 errors, and
0 skipped.

Evidence files:

- `test-before-refactor-log.txt`
- `test-before-refactor.png`
- `test-after-refactor-log.txt`
- `test-after-refactor.png`

These results support that public API behavior remained unchanged.

## 12. Git Commit History

The project history was organized into analysis, refactoring, and validation
commits.

| Commit | Purpose |
|---|---|
| `e1a9076` | Added PMD analysis evidence and prompt log |
| `0f31177` | Added initial project analysis evidence and before-refactor test evidence |
| `dd1a70f` | Added repository analysis and code-smell documentation |
| `005b76c` | Added refactoring target inspection |
| `077ba7e` | Refactored `CDL.rowToString(JSONArray, char)` |
| `e3c4878` | Added post-refactor validation evidence |

The incremental history supports the project requirement because it separates
evidence collection, analysis, source refactoring, and validation evidence into
reviewable steps.

## 13. Pull Request Submission

Phase 6 was completed by creating a clean pull request branch,
`pr/refactor-cdl-row-serialization`, containing only the source-code refactor
for `src/main/java/org/json/CDL.java`. The course/evidence branch remains
`refactor/code-smell-fixes`, and the project documentation files were not
included in the submitted pull request.

Submitted PR message points:

- Explain that the change is an internal readability refactor of
  `CDL.rowToString(JSONArray, char)`.
- Mention that public method signatures are unchanged.
- Explain that CSV row output behavior is preserved.
- Mention that `mvn test` passed after the refactoring.

Pull request submitted:
`https://github.com/stleary/JSON-java/pull/1062`

This report does not claim that a PR has been accepted.

## 14. Challenges Faced

The main challenge was choosing a target that was meaningful but not too risky.
PMD reported larger smells in `JSONObject` and `JSONArray`, but those classes
have broad public APIs and central responsibilities. Refactoring them would
create a larger compatibility risk.

Another challenge was separating tool findings from appropriate refactoring
targets. PMD findings were useful evidence, but manual analysis was needed to
decide which findings could be changed safely.

The selected method was a better fit because it had clear PMD and manual
evidence, a small scope, and behavior that could be validated by tests.

## 15. Reflection

This project showed that static-analysis results need human judgment. PMD
identified many possible smells, but not every finding was suitable for a
small, upstream-friendly refactoring. The final target was chosen because it
balanced measurable complexity with low behavioral risk.

The refactoring improved readability by naming the separate parts of the CSV
row-writing behavior. Instead of changing the public API or redesigning a large
class, the change made one method easier to understand while preserving its
observable output. The before and after Maven test evidence supports that the
behavior remained stable.

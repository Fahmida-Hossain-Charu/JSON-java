# Selected Code Smells

## Selection Strategy

The refactoring target should:

- Be supported by PMD and manual evidence.
- Be small enough to understand and validate.
- Preserve the public API and observable behavior.
- Avoid broad changes to a mature compatibility-sensitive library.
- Produce a clear before/after comparison for the assignment.

## Primary Refactoring Target

### `CDL.rowToString(JSONArray, char)`

Location: `src/main/java/org/json/CDL.java:179`

Recorded PMD findings:

- `CognitiveComplexity`: 21, above threshold 15.
- `CyclomaticComplexity`: 13.
- `AvoidDeeplyNestedIfStmts` near line 195.

Manual finding:

The method combines several responsibilities:

- Adding delimiters.
- Handling null values.
- Converting values to strings.
- Deciding whether a value requires quoting.
- Filtering characters inside quoted values.
- Writing quotes and the final newline.

Why selected:

- The smell is supported by both metric-based and manual evidence.
- It is a focused method-level refactoring.
- Internal helper extraction or control-flow simplification can improve
  readability without changing the public signature.
- The existing test baseline provides a way to validate unchanged behavior.

Required validation before any Phase 3 change:

- Identify existing `CDLTest` cases that cover quoting, delimiters, empty
  values, newlines, carriage returns, and troublesome characters.
- Add characterization tests only if behavior is not already covered.
- Compare output before and after refactoring.

## Secondary Candidate

### `CDL.getValue(JSONTokener, char)`

Location: `src/main/java/org/json/CDL.java:44`

Recorded PMD findings:

- `CognitiveComplexity`: 24, above threshold 15.
- `CyclomaticComplexity`: 15.
- `AvoidDeeplyNestedIfStmts` near line 61.

Why it is only secondary:

- It parses quoted and unquoted input and controls tokenizer backtracking.
- Small changes may affect malformed input, escaped quotes, delimiter handling,
  or EOF behavior.
- It should be refactored only if existing tests clearly characterize all
  relevant behavior and the primary target is completed safely.

## Documented but Not Selected

| Smell | Evidence | Why not selected |
|---|---|---|
| God Class in `JSONObject` | PMD: WMC 606, ATFD 124, TCC 1.480% | Splitting it would be a broad architectural change with high compatibility risk |
| God Class in `JSONArray` | PMD: WMC 273, ATFD 19, TCC 2.745% | Splitting it would affect a central public API and exceed the intended scope |
| Too Many Methods | PMD records 20 findings, including `CDL`, `JSONObject`, and `JSONArray` | Meaningful reduction would likely require API removal, movement, or broad redesign |
| Excessive public API | PMD reports 104 public members for `JSONObject` and 96 for `JSONArray` | Public API changes risk breaking users and are unsuitable for a behavior-preserving refactor |
| Broad unchecked-exception signature changes | PMD records 178 `AvoidUncheckedExceptionsInSignatures` findings | Removing declarations across public APIs would create a large noisy diff and may reduce intended documentation |
| Broad exception-handling cleanup | PMD records many generic exception catches | Each catch requires individual behavioral analysis; mass replacement could change fallback behavior |
| Large architectural class splitting | Manual and PMD evidence identify broad responsibilities | Too large, risky, and difficult to validate as a narrow upstream PR |

## Decision

Proceed to Phase 3 with `CDL.rowToString(JSONArray, char)` as the only primary
target. Consider `CDL.getValue(JSONTokener, char)` only after the primary change
is safely validated. All other findings remain documented analysis results, not
planned refactoring work.

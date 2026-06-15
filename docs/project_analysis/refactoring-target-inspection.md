# Refactoring Target Inspection

## Selected Target

- File: `src/main/java/org/json/CDL.java`
- Method: `public static String rowToString(JSONArray ja, char delimiter)`
- Approximate location: begins near `CDL.java:179`, with the deeply nested
  character-filtering conditional near line 195.

## Current Responsibility

The method converts a `JSONArray` into one delimiter-separated row ending with
a newline. It inserts delimiters, handles null values, converts values to text,
decides when quoting is required, filters troublesome characters from quoted
values, writes quotes, and appends the final newline.

## Smell Evidence

PMD reports:

- `CognitiveComplexity`: 21.
- `CyclomaticComplexity`: 13.
- `AvoidDeeplyNestedIfStmts` near line 195.

Manual analysis identifies:

- Complex conditional logic.
- A poorly structured method.
- Mixed responsibilities between formatting decisions, character filtering,
  and output construction.

## Selection Rationale

This target was selected because both PMD and manual analysis identify the same
maintainability problem. It is a small, localized method whose behavior can be
validated with focused tests. Refactoring it is safer than restructuring the
central `JSONObject` or `JSONArray` classes and safer than changing public API
exception behavior.

## Planned Refactoring

- Apply **Extract Method** to separate formatting decisions and quoted-value
  writing from row construction.
- Apply **Reduce Nested Conditional** to make the control flow easier to follow.
- Preserve the public method signature and API.
- Preserve the exact CSV row output, including delimiters, quoting, filtered
  characters, null handling, and the terminating newline.
- Validate unchanged behavior with existing tests and focused characterization
  tests where coverage is missing.

## Secondary Candidate

`CDL.getValue(JSONTokener, char)` is a secondary refactoring candidate, but it
is riskier because it controls parsing behavior. Changes could affect quoted
and unquoted values, malformed input, delimiters, tokenizer backtracking, and
end-of-input handling.

The requested `docs/project_analysis/pmd-code-smell-report.txt` evidence file
was not available; the PMD findings above are corroborated by the existing
`tool-analysis.md` and `selected-code-smells.md` documents.

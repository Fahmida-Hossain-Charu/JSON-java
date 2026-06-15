# Refactoring Summary

## Selected Smell and Target

The selected target was the complex and deeply nested CSV row-serialization
method:

- File: `src/main/java/org/json/CDL.java`
- Method: `public static String rowToString(JSONArray ja, char delimiter)`
- Approximate original location: `CDL.java:179`, with a deeply nested
  conditional near line 195.

PMD reported:

- `CognitiveComplexity`: 21, above the threshold of 15.
- `CyclomaticComplexity`: 13.
- `AvoidDeeplyNestedIfStmts` near line 195.

Manual analysis identified complex conditional logic, poor method structure,
mixed responsibilities, and nested conditional logic. The method performed row
construction, null handling, string conversion, quote decisions, quoted-value
filtering, and quote writing in one block.

## Refactoring Technique

Commit `077ba7e` (`Refactor CDL row serialization`) applied **Extract Method**
and reduced the nesting inside `rowToString`. It introduced three private
static helpers:

- `appendRowValue(StringBuilder sb, Object object, char delimiter)`
- `shouldQuoteValue(String string, char delimiter)`
- `appendQuotedValue(StringBuilder sb, String string)`

## Maintainability Improvement

The public method now focuses on row-level behavior: iterating over values,
placing delimiters, delegating value formatting, and appending the final
newline. The helper names make null handling, quote decisions, and quoted-value
filtering explicit. This separates responsibilities and makes each part easier
to read, review, and modify independently.

## Behavior Preservation

The public method signature was not changed. The original quote-decision
condition and quoted-character filtering condition were moved into helpers
without changing their logic. Delimiter placement and the terminating newline
remain in the public method, and null values still produce no value text.

The saved before and after `mvn test` logs both report 777 tests, 0 failures,
0 errors, 6 skipped, and `BUILD SUCCESS`, supporting that public API behavior
remained unchanged.

# Before and After Comparison

## Before Refactoring

Before commit `077ba7e`, `CDL.rowToString(JSONArray, char)` contained the full
row-serialization process. Inside its main loop, it placed delimiters, handled
nulls, converted objects to strings, decided whether quoting was required,
filtered characters from quoted values, and wrote the surrounding quotes.

The nested loop and conditionals made the method difficult to scan because
row-level control flow and value-level formatting were combined.

## After Refactoring

After the refactoring, `rowToString` retains only row-level coordination. For
each array element, it places the delimiter when needed and delegates value
formatting to `appendRowValue`. The quote decision and quoted-value writing are
handled by dedicated private static helpers.

## Comparison

| Concern | Before | After |
|---|---|---|
| Row construction | Mixed with all value-formatting details | Remains in `rowToString` |
| Null handling | Nested inside the main loop | Early return in `appendRowValue` |
| Quote decision | Long conditional inside the main loop | Named `shouldQuoteValue` helper |
| Quoted-value filtering | Nested character loop inside the main loop | Isolated in `appendQuotedValue` |
| Method structure | Multiple responsibilities and nested conditionals | Responsibilities separated by named helpers |
| Public API | `rowToString(JSONArray, char)` | Unchanged |

## Extracted Helpers

| Helper | Responsibility |
|---|---|
| `appendRowValue(StringBuilder, Object, char)` | Handles one value, including null handling, string conversion, and choosing quoted or unquoted output |
| `shouldQuoteValue(String, char)` | Applies the existing condition that determines whether a value must be quoted |
| `appendQuotedValue(StringBuilder, String)` | Writes surrounding quotes and applies the existing character filter inside quoted values |

## Logic Preservation

The refactoring moved logic rather than changing it:

- The delimiter is still appended between array positions.
- Null values still append no value text.
- Values are still converted with `object.toString()`.
- The original quote-decision expression is unchanged.
- The original `c >= ' ' && c != '"'` character filter is unchanged.
- The final newline is still appended after the row.

The commit changes only the internal organization of `CDL.rowToString` and
adds private helpers; no public method signature was changed.

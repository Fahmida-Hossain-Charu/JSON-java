# Manual Code-Smell Analysis

Manual analysis complements PMD by examining responsibilities, control flow,
behavioral risk, and whether a finding is appropriate for this project.

| Smell type | File/method location | Manual evidence | Why it is problematic | Selected for refactoring? | Reason |
|---|---|---|---|---|---|
| Complex method | `CDL.rowToString(JSONArray, char)` at `CDL.java:179` | One method handles delimiter insertion, null handling, conversion to text, quote-decision logic, character filtering, quote writing, and newline termination | Multiple responsibilities and nested branches make the formatting behavior harder to understand and modify safely | Yes | Narrow, measurable, and behavior can be preserved with focused tests |
| Deeply nested conditionals | `CDL.rowToString(JSONArray, char)`, especially the quoting/filtering branch near `CDL.java:195` | The method nests loop, index condition, null condition, quote condition, inner character loop, and character filter | Readers must track several conditions simultaneously; changes to quoting rules may introduce regressions | Yes | This is part of the primary target and can be simplified without changing the public API |
| Complex parser method | `CDL.getValue(JSONTokener, char)` at `CDL.java:44` | The method handles whitespace skipping, EOF, quoted input, escaped quotes, malformed quotes, delimiters, tokenizer backtracking, and unquoted input | Parsing edge cases are concentrated in one control-flow-heavy method | Secondary candidate only | Valuable smell, but parser changes are more behavior-sensitive than output formatting |
| Large/God Class | `JSONObject` | The class combines parsing, storage, reflection-based bean conversion, numeric conversion, wrapping, validation, lookup, and serialization | Many responsibilities increase coupling and make broad changes difficult to reason about | No | Splitting the class would be a large architectural and public-API-sensitive change |
| Large/God Class | `JSONArray` | The class combines parsing, storage, conversions, typed accessors, collection operations, similarity checks, and serialization | The class has broad responsibility and a large public surface | No | Architectural splitting is too broad for a focused upstream refactoring |
| Too many public methods | `JSONObject` and `JSONArray` | Both expose many constructors, accessors, conversion methods, and serialization methods | A large public API increases testing combinations and restricts internal redesign | No | Removing or reorganizing public methods risks backward compatibility |
| Repeated parsing/conversion logic | `JSONObject.stringToValue`, `XML.stringToValue`, `JSONTokener.nextValue`, and wrapping/conversion paths | Several components independently classify or convert textual and Java values into JSON-compatible values | Similar conversion responsibilities can become inconsistent and are harder to change safely across formats | No | Consolidation would affect multiple formats and behavior contracts; document for future work |
| Exception-handling concerns | Multiple `catch (Exception ...)` locations in `JSONObject` and `JSONArray`; ignored reflection exceptions in `JSONObject.processMethod` | Broad catches and ignored exceptions make it difficult to distinguish expected fallback behavior from hidden failures | Generic or swallowed exceptions can obscure defects and complicate debugging | No | Many catches may be intentional compatibility behavior; each requires separate behavioral analysis |
| Too Many Methods | `CDL`, `JSONObject`, and `JSONArray` | Each class provides multiple related public operations and overloads | Large method counts can indicate broad responsibility and increase maintenance effort | No, except the selected method-level cleanup | Reducing method count would likely require API changes or broad redesign |

## Primary Manual Finding

`CDL.rowToString(JSONArray, char)` is the best Phase 3 candidate because the
manual inspection confirms the same concern reported by PMD:

- It contains several nested decisions.
- It mixes decision-making with output construction.
- It is publicly reachable but can be internally reorganized without changing
  its signature or expected output.

## Manual Versus Tool Findings

PMD identifies metric thresholds and suspicious patterns. Manual analysis adds
context by deciding:

- Whether the finding represents a real maintainability concern.
- Whether changing it would preserve behavior.
- Whether the change is small enough for the assignment and an upstream PR.
- Whether compatibility risk is greater than the potential design benefit.

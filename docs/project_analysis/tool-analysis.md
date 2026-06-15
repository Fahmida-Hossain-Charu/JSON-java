# Tool-Based Code-Smell Analysis

## Tool and Scope

- Tool: PMD
- Analysis focus: Design/code-smell rules
- Ruleset recorded in the text report: `category/java/design.xml`
- PMD version recorded in the text report: 7.21.0
- Scope recorded in the text report: 85 Java files from production and test
  sources

## Recorded Results

Two PMD evidence artifacts are present:

| Evidence | Recorded total |
|---|---:|
| `intellij_pmd_code_smell.png` | 470 violations |
| `pmd_code_smell.txt` | 475 violations |

The totals differ by five. They should be treated as separate runs/settings,
not silently combined. The screenshot is the clearest visual evidence of the
IntelliJ run, while the text report provides detailed locations and
explanations.

## Main Smell Categories

The detailed text report records the following notable categories:

| PMD smell | Count |
|---|---:|
| `AvoidUncheckedExceptionsInSignatures` | 178 |
| `AvoidCatchingGenericException` | 73 |
| `CyclomaticComplexity` | 52 |
| `AvoidDeeplyNestedIfStmts` | 34 |
| `CognitiveComplexity` | 25 |
| `TooManyMethods` | 20 |
| `NcssCount` | 11 |
| `GodClass` | 10 |
| `SimplifyBooleanExpressions` | 10 |
| `UseUtilityClass` | 8 |
| `ExcessivePublicCount` | 7 |
| `CouplingBetweenObjects` | 2 |

## Important Examples

### `CDL.java`

- `CDL.getValue(JSONTokener, char)`:
  - Cognitive complexity: 24, above PMD threshold 15.
  - Cyclomatic complexity: 15.
  - Deeply nested conditional reported near line 61.
- `CDL.rowToString(JSONArray, char)`:
  - Cognitive complexity: 21, above PMD threshold 15.
  - Cyclomatic complexity: 13.
  - Deeply nested conditional reported near line 195.
- `CDL` is also reported for `TooManyMethods` and `UseUtilityClass`.

### `JSONArray.java`

- Reported as a possible God Class:
  - WMC: 273.
  - ATFD: 19.
  - TCC: 2.745%.
- Total class cyclomatic complexity: 273; highest method complexity: 18.
- `ExcessivePublicCount`: 96 public methods and attributes, above threshold 45.
- Reported for `TooManyMethods`.
- Multiple generic exception catches are reported.

### `JSONObject.java`

- Reported as a possible God Class:
  - WMC: 606.
  - ATFD: 124.
  - TCC: 1.480%.
- Coupling Between Objects value: 39, above threshold 20.
- Total class cyclomatic complexity: 606; highest method complexity: 28.
- `ExcessivePublicCount`: 104 public methods and attributes, above threshold 45.
- Reported for `TooManyMethods`.
- Multiple complex methods and generic exception catches are reported.

## Interpretation

PMD is useful for finding candidates and providing measurable evidence.
However, a PMD violation is not automatically a defect or an appropriate
refactoring target.

Examples:

- A broad public API may be intentional in a mature compatibility-sensitive
  library.
- An unchecked exception declaration may be retained for documentation or
  compatibility.
- Splitting `JSONObject` or `JSONArray` could create a large, risky change even
  though PMD reports God Class and Too Many Methods.

## Tool Analysis Versus Manual Analysis

| Tool-based analysis | Manual analysis |
|---|---|
| Applies rule thresholds consistently across many files | Examines responsibilities and behavior in context |
| Produces counts, metrics, and exact locations | Determines whether a finding is meaningful and safe to change |
| Finds broad candidate sets quickly | Selects a narrow, defensible refactoring target |
| May include false positives or compatibility-sensitive findings | Accounts for tests, API stability, and upstream acceptance risk |

The selected target was therefore not chosen only because PMD reported it.
`CDL.rowToString(JSONArray, char)` was selected because both PMD and manual
inspection identify complexity, and the method can be improved within a narrow
scope.

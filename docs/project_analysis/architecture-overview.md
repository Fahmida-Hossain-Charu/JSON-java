# Architecture Overview

## System Summary

JSON-java is a compact, library-style Java project that implements JSON parsing,
construction, navigation, and serialization. It also includes adapters for XML,
comma-delimited lists, cookies, HTTP headers, and Java properties.

The project does not have a traditional application `main` method. Client code
enters the library through public APIs such as `new JSONObject(String)`,
`new JSONArray(String)`, `XML.toJSONObject(String)`, and `CDL.toJSONArray(String)`.

## Project Structure

| Location | Purpose |
|---|---|
| `src/main/java/org/json` | Production library implementation |
| `src/test/java/org/json/junit` | JUnit tests |
| `src/test/java/org/json/junit/data` | Test beans and fixtures |
| `src/test/resources` | JSON and XML parsing fixtures |
| `pom.xml` | Maven build configuration |
| `build.gradle`, `gradlew`, `gradlew.bat` | Gradle build configuration and wrapper |
| `docs`, `Examples.md` | Project documentation and usage examples |

All production classes are contained in the main package `org.json`.

## Main Components

| Component | Responsibility |
|---|---|
| `JSONObject` | Map-like representation of a JSON object; parsing, conversion, lookup, reflection-based wrapping, and serialization |
| `JSONArray` | Ordered representation of a JSON array; parsing, collection conversion, lookup, and serialization |
| `JSONTokener` | Character-level tokenizer used by JSON parsers; reads strings, values, whitespace, and syntax positions |
| `XML` | Converts between XML text and the `JSONObject`/`JSONArray` model |
| `CDL` | Converts comma-delimited rows and tables to and from JSON arrays and objects |
| `Cookie`, `CookieList` | Convert cookie text to and from JSON objects |
| `HTTP`, `HTTPTokener` | Convert HTTP headers to and from JSON objects |
| `JSONWriter` | Stateful streaming JSON writer |
| `JSONStringer` | `JSONWriter` specialization that writes into a string |
| `JSONPointer` | Navigates values inside `JSONObject` and `JSONArray` using RFC 6901 pointers |
| `JSONException` | Common unchecked exception for parsing and conversion failures |

## Key Relationships

```text
String / Reader / InputStream
            |
            v
       JSONTokener
            |
       nextValue()
            |
      +-----+------+
      |            |
      v            v
 JSONObject     JSONArray
 Map-like       List-like
      |            |
      +-----+------+
            |
            v
 nested JSON values and scalar values

JSONPointer ------> navigates JSONObject / JSONArray
JSONWriter -------> serializes JSON values
JSONStringer -----> extends JSONWriter
XMLTokener -------> extends JSONTokener
HTTPTokener -----> extends JSONTokener
XML / CDL / HTTP / Cookie ---> adapt external formats to the JSON model
```

## JSON Parsing Flow

The typical flow for `new JSONObject(jsonText)` is:

1. The input string is wrapped in a `JSONTokener`.
2. `JSONObject` checks the opening object delimiter.
3. The tokenizer reads each key and the required key/value separator.
4. `JSONTokener.nextValue()` dispatches based on the next token:
   - `{` creates a nested `JSONObject`.
   - `[` creates a nested `JSONArray`.
   - A quote begins string parsing.
   - Other text is interpreted as a scalar value.
5. Scalar text is converted into a Boolean, number, `JSONObject.NULL`, or
   String.
6. Parsed key/value pairs are placed in the `JSONObject` backing map.
7. Parsing continues until the closing object delimiter.

Array parsing follows the same recursive approach. `JSONArray` repeatedly calls
`JSONTokener.nextValue()` and stores each parsed result in order.

## Library-Style Execution Flow

Because JSON-java is a reusable library, its execution flow depends on the API
selected by the caller.

```text
Client application
    |
    +--> JSONObject / JSONArray constructors
    |       |
    |       +--> JSONTokener --> nested JSON model
    |
    +--> XML / CDL / HTTP / Cookie conversion APIs
    |       |
    |       +--> specialized tokenizer or conversion logic
    |       +--> JSONObject / JSONArray model
    |
    +--> JSONWriter / toString / write
            |
            +--> serialized JSON text
```

For this project, Phase 1 describes public API entry points and parser/converter
flows instead of identifying a single executable `main` method.

## Architectural Observations Relevant to Phase 2

- `JSONObject` and `JSONArray` are central classes with many public methods and
  several responsibilities.
- Parsing and conversion are distributed across `JSONTokener`, `JSONObject`,
  `JSONArray`, `XML`, and `CDL`.
- The single-package architecture is easy to navigate, but it also places many
  responsibilities and public APIs close together.
- Existing tests provide a strong baseline for behavior-preserving refactoring.

Evidence: `pmd_code_smell.txt`, `test-before-refactor-log.txt`, and direct
inspection of the production classes.

## Architecture Overview

  JSON-java is a compact Java 8 library. All production classes are contained in the single org.json package.

  ### Project Structure

   Location                             Purpose
  ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━  ━━━━━━━━━━━━━━━━━━━━━━━━━━━━
   src/main/java/org/json               Library implementation
  ───────────────────────────────────  ────────────────────────────
   src/test/java/org/json/junit         JUnit 4 tests
  ───────────────────────────────────  ────────────────────────────
   src/test/java/org/json/junit/data    Test beans and fixtures
  ───────────────────────────────────  ────────────────────────────
   src/test/resources                   JSON/XML parsing fixtures
  ───────────────────────────────────  ────────────────────────────
   pom.xml, build.gradle                Maven and Gradle builds
  ───────────────────────────────────  ────────────────────────────
   docs, Examples.md                    Documentation and examples

  ### Main Class Groups

  Core JSON model

  - src/main/java/org/json/JSONObject.java:77: Map-like JSON object backed by Map<String, Object>.
  - src/main/java/org/json/JSONArray.java:63: Ordered JSON array backed by ArrayList<Object>.
  - src/main/java/org/json/JSONTokener.java:17: Streaming character reader and recursive parser dispatcher.
  - JSONException: Common unchecked exception.
  - JSONParserConfiguration: Controls strict parsing and duplicate-key behavior.

  Navigation and serialization

  - JSONPointer: RFC 6901 navigation across JSONObject and JSONArray.
  - JSONWriter: Stateful streaming JSON writer.
  - JSONStringer: JSONWriter specialization that writes into a string.
  - JSONString: Interface allowing objects to provide custom JSON serialization.

  Format adapters

  - src/main/java/org/json/XML.java:22, XMLTokener, XMLParserConfiguration: XML-to-JSON conversion.
  - JSONML: JSONML/XML conversion.
  - CDL: Comma-delimited data conversion.
  - HTTP, HTTPTokener: HTTP header conversion.
  - Cookie, CookieList: Cookie conversion.
  - Property: Java Properties conversion.

  ## Key Relationships

  String / Reader / InputStream
            |
            v
       JSONTokener
            |
            | nextValue()
            v
    +-------------------+
    |                   |
  JSONObject         JSONArray
  Map<String,Object> ArrayList<Object>
    |                   |
    +---- nested values-+
            |
            v
   Scalars: String, Boolean, Number, JSONObject.NULL

  JSONPointer ---------> navigates JSONObject / JSONArray
  JSONWriter ----------> serializes JSONObject / JSONArray
  XMLTokener ----------> extends JSONTokener
  XML -----------------> builds JSONObject / JSONArray from XML

  ### JSONObject

  JSONObject is the central object representation. It stores properties in a deliberately unordered HashMap and uses
  JSONObject.NULL to represent JSON null distinctly from Java null (src/main/java/org/json/JSONObject.java:128).

  It can be constructed from:

  - JSON text or a JSONTokener
  - Java maps, beans, records, and resource bundles
  - Other JSONObject instances

  Nested Java collections, maps, arrays, and beans are converted through JSONObject.wrap() (src/main/java/org/json/
  JSONObject.java:2938).

  ### JSONArray

  JSONArray stores ordered values in an ArrayList<Object> (src/main/java/org/json/JSONArray.java:68). Its parser
  repeatedly calls JSONTokener.nextValue() and adds the resulting nested containers or scalars (src/main/java/org/json/
  JSONArray.java:96).

  ### JSONTokener

  JSONTokener wraps a Reader and tracks the current index, line, character, previous character, and EOF state (src/main/
  java/org/json/JSONTokener.java:17).

  Its main responsibilities are:

  - next() reads one character.
  - back() supports one-character look-behind.
  - nextClean() skips whitespace.
  - nextString() parses quoted strings and escape sequences.
  - nextValue() dispatches to JSONObject, JSONArray, or scalar parsing.
  - syntaxError() creates location-aware errors.

  ### XML

  XML is a static conversion utility rather than a model class. It uses XMLTokener, recursively processes XML elements,
  and builds the same JSONObject/JSONArray object model used by JSON parsing (src/main/java/org/json/XML.java:787, src/
  main/java/org/json/XML.java:251).

  Repeated XML tags are represented using JSONObject.accumulate(), which promotes repeated values into arrays.

  ## JSON Parsing Flow

  For new JSONObject(jsonText):

  1. The string is wrapped in a StringReader, then a JSONTokener.
  2. JSONObject verifies the first non-whitespace character is {.
  3. Each key is read using JSONTokener.nextSimpleValue().
  4. The parser requires : after the key.
  5. JSONTokener.nextValue() parses the value:
      - { creates a nested JSONObject.
      - [ creates a nested JSONArray.
      - Quotes invoke nextString().
      - Other text is parsed as a scalar.

  6. Scalar text is converted by JSONObject.stringToValue() into Boolean, JSONObject.NULL, a numeric type, or String
     (src/main/java/org/json/JSONObject.java:2679).

  7. The resulting value is inserted into the backing map.
  8. Parsing continues until } is reached.

  Parsing arrays follows the same recursive process, ending at ].

  By default, parsing is lenient and accepts features such as single-quoted strings, unquoted values, trailing commas,
  and semicolon object separators. JSONParserConfiguration strict mode rejects these extensions and also checks
  duplicate keys and unparsed trailing content.
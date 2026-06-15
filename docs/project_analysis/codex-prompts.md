# Codex and DeepSeek Prompt Log

Repository: JSON-java  
Fork: Fahmida-Hossain-Charu/JSON-java  
Branch: refactor/code-smell-fixes  
Project: Code Smells in the Wild  

This file records all prompts used for repository understanding, code smell analysis, refactoring, validation, documentation, and PR preparation.

## Prompt 1 — Architecture overview

Tool: Codex in VS Code
Date: Monday, 10:28 PM
Purpose: Generate repository architecture overview for Phase 1 documentation.

Exact prompt used:

```text
Analyze the JSON-java project structure. Create an architecture overview including: main packages, key classes (JSONObject, JSONArray, JSONTokener, XML), their relationships, and a description of how the library parses JSON.
```

Output generated:
Codex produced an architecture overview describing the project structure, main `org.json` classes, relationships between `JSONObject`, `JSONArray`, `JSONTokener`, and `XML`, and the general JSON parsing flow.

Output saved as:
`docs/project_analysis/architecture-overview.md`
## Prompt 2 — Run PMD and save report

Tool: Codex in VS Code
Purpose: Find command-line method to run PMD and save output.

Exact prompt used:

```text
so how do i run pmd in current directory & also save the output as code smell.txt in this dir
```

Codex output summary:
Codex suggested running Maven PMD and redirecting the output to `code smell.txt`.

Result:
The initial command did not fully match the IntelliJ PMD report, because Maven PMD used a different/default configuration.

---

## Prompt 3 — Fix PMD command error

Tool: Codex in VS Code
Purpose: Fix PMD command/run issue.

Exact prompt used:

```text
ok didn't run pmd properly has some errors so fix it
```

Codex output summary:
Codex suggested using Maven PMD output from `target/pmd.txt` and copying it into `code smell.txt`.

Result:
This produced a PMD report, but it did not match the IntelliJ PMD design-rule output.

---

## Prompt 4 — Compare Maven PMD and IntelliJ PMD reports

Tool: Codex in VS Code
Purpose: Understand why Maven PMD output differed from IntelliJ PMD output.

Exact prompt used:

```text
check if code smell.txt is right cause in intellij there are 470 violations in pmd extension also it had specified code smells in those violations like which code smell is that violation why it's not the same here
```

Codex output summary:
Codex found that Maven PMD and IntelliJ PMD were using different configurations. Codex attempted to align the Maven PMD configuration by editing `pom.xml`.

Result:
The `pom.xml` edit was rejected because original project build files should not be changed for tool-report generation. The change was undone/restored.

---

## Prompt 5 — Repository suitability check

Tool: Codex in VS Code
Purpose: Check whether JSON-java satisfies project requirements.

Exact prompt used:

```text
ok so check if this json java repo meets all requirements marking criteria & all phases tasks here is it a good fit for project?
```

Codex output summary:
Codex assessed JSON-java as a suitable repository for the project. It identified that the repository is public, Java-based, actively maintained, has many stars/commits, builds locally, and has enough complexity for analysis. It also noted that large unsolicited refactoring PRs are risky.

Result:
This supported the repository selection justification section.

---

## Prompt 6 — Correct PMD code-smell report scope

Tool: Codex in VS Code
Purpose: Clarify that the required PMD output should focus on code smells/design issues, not general code style.

Exact prompt used:

```text
why didn't u identify the code smells there why did u do code style design these are not code smells read the project pdf then generate the code smell.txt running a static analysis via pmd ..see in intellij it showed exact code smell like god class long method long parameter list i wanted u to run pmd then have actual code smells in that txt file like the intellij one
```

Codex output summary:
Codex attempted further PMD-related commands, but the run was stopped before completion.

Result:
The final PMD evidence used for the project is the IntelliJ PMD design/code-smell report already saved under `docs/project_analysis/`, not a modified Maven build configuration.
## Prompt — Phase 1 and Phase 2 documentation review

Tool: Codex in VS Code  
Purpose: Review and complete project documentation for repository selection, architecture, manual analysis, tool analysis, and selected code smells.

Exact prompt used:
```text
I am working on a software engineering project called "Code Smells in the Wild" using the repository stleary/JSON-java.

Your task is to review and complete the Phase 1 and Phase 2 documentation only.

IMPORTANT RULES:

* Do not modify source code.
* Do not modify pom.xml, build.gradle, README, or any project configuration.
* Do not delete existing files.
* Do not overwrite existing useful content.
* Only create or update files inside docs/project_analysis/.
* Use existing evidence already present in docs/project_analysis/.
* Do not invent results.
* If evidence is missing, write "Evidence to be added" instead of guessing.

First inspect the existing files inside docs/project_analysis/.

Then create or update these files only if needed:

1. docs/project_analysis/project-evidence-index.md
   Create an index of all evidence files and explain what each file proves.

2. docs/project_analysis/repository-selection.md
   Explain why JSON-java satisfies the assignment repository requirements:

* public GitHub repository,
* Java project,
* actively maintained,
* sufficient stars and commits,
* manageable size,
* build/test support,
* suitable for code smell analysis,
* risk of upstream PR acceptance because large refactors may not be accepted.

3. docs/project_analysis/architecture-overview.md
   If this file already exists, improve it only if needed.
   Include:

* project structure,
* main package org.json,
* key classes: JSONObject, JSONArray, JSONTokener, XML, CDL, Cookie, HTTP, JSONWriter,
* relationships between classes,
* JSON parsing flow,
* library-style execution flow instead of a traditional main method.

4. docs/project_analysis/manual-analysis.md
   Create a manual code-smell analysis table with:

* smell type,
* file/method location,
* manual evidence,
* why it is problematic,
* selected for refactoring yes/no,
* reason.

Include:

* complex methods,
* deeply nested conditionals,
* large classes / god classes,
* too many public methods,
* repeated parsing/conversion logic,
* exception-handling concerns.

5. docs/project_analysis/tool-analysis.md
   Summarize the PMD/static-analysis evidence already present in docs/project_analysis.
   Include:

* tool name: PMD,
* design/code-smell analysis,
* total violations if available,
* main categories,
* important examples from CDL.java, JSONArray.java, JSONObject.java,
* how tool-based analysis differs from manual analysis.

6. docs/project_analysis/selected-code-smells.md
   Explain selected and non-selected smells.
   Primary refactoring target:

* CDL.rowToString(JSONArray, char)

Secondary candidate:

* CDL.getValue(JSONTokener, char), only if safe.

Document but do not refactor:

* GodClass in JSONObject/JSONArray,
* TooManyMethods,
* broad public API exception signature changes,
* large architectural class splitting.

After completing, summarize exactly which files you created or updated.
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

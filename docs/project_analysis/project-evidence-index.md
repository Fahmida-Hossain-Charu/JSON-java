# Project Evidence Index

This index lists the evidence currently stored in `docs/project_analysis/` and
explains what each artifact proves. Claims not supported by an artifact are
marked as requiring additional evidence.

| Evidence file | What it proves | Relevant phase |
|---|---|---|
| `architecture-overview.md` | Documents project structure, major classes, relationships, JSON parsing flow, and library-style entry points | Phase 1 |
| `repository-selection.md` | Maps JSON-java to the repository-selection requirements and records selection risks | Phase 1 |
| `manual-analysis.md` | Records manually inspected code smells, their locations, impact, and selection decisions | Phase 2 |
| `tool-analysis.md` | Summarizes PMD results and explains the relationship between tool findings and manual analysis | Phase 2 |
| `selected-code-smells.md` | Records the selected refactoring target, secondary candidate, and intentionally excluded smells | Phase 2 planning |
| `pmd_code_smell.txt` | Detailed PMD 7.21.0 Design-ruleset report. It records 475 findings across 85 Java files and includes rule, location, priority, explanation, and documentation link | Phase 2 tool analysis |
| `intellij_pmd_code_smell.png` | Screenshot of IntelliJ PMD results showing 470 Design-ruleset violations across 85 scanned files and visible rule counts | Phase 2 tool evidence |
| `test-before-refactor-log.txt` | Maven baseline test execution: 777 tests, 0 failures, 0 errors, 6 skipped, and `BUILD SUCCESS` | Phase 1 setup and future Phase 4 baseline |
| `test-before-refactor.png` | Visual evidence of the successful baseline test run | Phase 1 setup and future Phase 4 baseline |
| `test-after-refactor-log.txt` | Maven post-refactor test execution: 777 tests, 0 failures, 0 errors, 6 skipped, and `BUILD SUCCESS` | Phase 4 validation |
| `test-after-refactor.png` | Visual evidence of the successful post-refactor test run | Phase 4 validation |
| `pr-submission-evidence.png` | Screenshot evidence that pull request `https://github.com/stleary/JSON-java/pull/1062` was submitted | Phase 6 pull request submission |
| `pr-review-response-evidence.png` | Screenshot evidence that the PR branch was updated after maintainer review comments | Phase 6 maintainer review response |
| `pull-request-submission.md` | Records the submitted PR URL, branch details, change summary, validation command, and pending maintainer-review status | Phase 6 pull request submission |
| `codex-chat-2-notes.md` | Records the PMD-report generation investigation and the decision not to modify project build configuration for evidence generation | Process evidence |
| `codex-prompts.md` | Records prompts used with Codex for architecture, PMD investigation, and repository suitability | Process/reflection evidence |
| `copilot-prompts-from-export.md` | Records prompts extracted from the Copilot export | Process/reflection evidence |
| `copilot-chat-readable-transcript.md` | Human-readable record of repository search, suitability discussion, initial code-smell observations, and scope decisions | Repository-selection and process evidence |

## Evidence Gaps

- Screenshot or exported GitHub page showing the current star count:
  **Evidence to be added**.
- Screenshot or exported GitHub page showing current upstream activity and
  commit count: **Evidence to be added**.
- Package/module diagram image suitable for the final submitted report:
  **Evidence to be added**. The text diagram is currently in
  `architecture-overview.md`.
- Manual-analysis screenshots with highlighted source sections:
  **Evidence to be added**.

## PMD Count Note

The saved artifacts record two close but different totals:

- `intellij_pmd_code_smell.png`: 470 violations.
- `pmd_code_smell.txt`: 475 violations.

These should be presented as separate PMD runs/settings. The evidence does not
prove that the two outputs are identical.

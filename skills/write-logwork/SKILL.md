---
name: write-logwork
description: >
  Write a logwork summary file for a branch/feature: clean, self-contained (standalone),
  so an outside reader understands what the branch does, how it is implemented, and what
  it solves. Use when the user says "write logwork", "summarize this branch", "log what was
  implemented", "create a feature summary doc". Focus on problem → solution, and do NOT
  reference prior phases/stages (avoid wording like "after P1", "compared to the old version").
---

# Write Logwork

Goal: produce a Markdown file that summarizes a branch/feature so that **an outside reader can
follow it start to finish** without knowing any prior history.

## Process

1. **Gather facts from the code, do not guess.**
   - `git diff HEAD --stat` (or `git diff <base>...HEAD --stat`) to scope the changes.
   - `git diff` on the important files; read new files in full.
   - If there are related design/plan/issue docs in the repo, read them for problem context —
     but **rewrite in the logwork's own voice**, do not copy the "plan" tone.
   - If many files need reading, delegate to a sub-agent and take the summary.

2. **Find the narrative spine:** what is the root problem → what is the solution → what does it
   prevent/achieve.

3. **Write using the standard structure below.**

4. **Place the file:** `docs/LOGWORK_<Short_Name>.md` (or the relevant module's docs folder).

## Required principles

- **Standalone.** No references to earlier stages ("after P1", "compared to the old version",
  "previously"). Write in present tense: "this branch implements...", "mechanism X works...".
- **Problem → Solution.** Every technical item must answer: what problem it solves, and how.
  Do not just list code changes dryly.
- **Understandable to outsiders.** Explain jargon on first use. Prefer "why" over "what".
- **Honest.** State clearly what was verified, what remains, and real limitations. No sugarcoating.
- **Grounded in real code.** Class/function/file names must match the source; verification
  results must be things actually run, never invented.
- **Language:** follow the language the user is using; keep code identifiers as-is.

## Standard file structure

```markdown
# Logwork — <Descriptive feature title>

> Branch: `<branch>` · Status: <implemented / in progress / verified ...>

## 1. What this branch solves
<Short context (what the system/module is). The core problem. The branch's goal.>

## 2. How it works — <name of the core idea/architecture>
<Overall idea. A table or flow diagram to make it easy to grasp.>

## 3. How each component is implemented
### 3.x <Component name> — <role>
<For each part: what problem it solves + how. A short snippet if it helps.>

## 4. Results — <what it achieves/prevents>
<Table: situation → result. For security: attack scenario → is it blocked.>

## 5. Verified
<List the checks actually run: build, tests, runtime...>

## 6. Limitations & remaining work
<Frank: what is not closed yet, dependencies, next steps.>

## 7. Files in this branch
<Table: file → role.>
```

Drop any section that does not fit the specific feature — keep the spirit: problem → how →
results → limitations.

## Summarizing style — the condensed technical summary

Besides the full document, produce a **condensed summary block**: a single tight paragraph of
intent followed by a nested bullet list of the concrete changes. Use it as the opening TL;DR of
the logwork, or as the PR/commit description. It must stand alone — a reader who skims only this
block should still grasp what the branch does and how.

Rules for the summary block:

- **Lead with intent, not mechanics.** Open with 1–2 sentences stating what the branch implements
  and the problem it defeats — the "so what", before the file list.
- **Group by concept, not by file.** Bullets are named by architectural role (an anchor, a native
  module, a build concern), and each names the real class/file it maps to.
- **Nest cause → effect.** Each top-level bullet is a component; sub-bullets say what it does and
  *why that matters* (e.g. "runs before `Application.onCreate`, so it catches the bypass where the
  custom Application is stripped from the manifest").
- **Concrete and grounded.** Real identifiers (`CShieldProvider`, `nativeGuard`, `lifecycle.cpp`),
  never vague ("some checks", "various files").
- **Still standalone.** No "after P1", no "compared to before" — describe the end state in present
  tense.

### Example

> Implement a three-point independent boot mechanism to prevent SDK bypass via repackaging or
> runtime hooking. The SDK now automatically initializes through the application lifecycle rather
> than explicit Java calls.
>
> - **Anchor 1 (`CShieldApplication`)**: A base `Application` class using a native
>   `attachBaseContext` implementation to perform early signature verification and SDK bootstrapping.
> - **Anchor 2 (`CShieldProvider`)**: A `ContentProvider` that triggers a `nativeGuard` check.
>   Since `ContentProvider` is initialized by the ART before `Application.onCreate` and
>   independently of the `Application` class, it detects bypasses where the custom `Application`
>   name is removed from the manifest.
> - **Anchor 3 (`CShieldActivity`)**: A base `Activity` class that performs redundancy checks
>   during business logic execution, forcing attackers to modify application code rather than just
>   manifest files.
> - **Native Implementation**:
>     - Introduced `lifecycle.cpp` to handle native-level lifecycle hooks and `nativeGuard`.
>     - Implemented `nativeGuard` to verify application signature and ensure the active
>       `Application` instance is a subclass of `CShieldApplication`.
>     - Obfuscated JNI registrations for all anchors and `LicenseBridge` methods to prevent symbol
>       grepping and hooking.
> - **SDK & Build**:
>     - Refactored `CShieldSDK.bootstrap` to be idempotent and handle different context types.
>     - Updated `AndroidManifest.xml` to include the initialization provider.
>     - Added comprehensive R8/ProGuard keep-rules for native anchors and license models to prevent
>       runtime crashes after code shrinking.
>     - Updated sample app to demonstrate the new integration flow.

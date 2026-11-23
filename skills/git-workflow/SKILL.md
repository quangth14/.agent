---
name: git-workflow
description: git conventions — branch naming, commit message style, and the rebase + no-ff merge into develop. Use when asked to create a branch, commit, split changes into branches, or merge/rebase work into develop.
---

# git workflow

Follow this when creating branches, writing commits, or landing work on `develop`.
Learned from the repo history (`git log --graph develop`); re-check it if unsure.

## Branches

- Work lands on `develop`. if not exist `develop` auto create this first.
- `master` is the release branch — never commit or merge into it unless asked.
- One branch per change, cut from the current tip of `develop`.
- Name: `<type>/<snake_case_summary>`, e.g. `feat/implement_javax_bluetooth`,
  `fix/dialog_shows_system_bars`, `chore/bump_version_code_13`.
  Types in use: `feat`, `fix`, `chore`, `refactor`, `bugfix` (rare, prefer `fix`).
- Keep the summary short and descriptive of the outcome, lowercase, words joined by `_`.

## Commits

- **One commit per branch, and a branch contains only code related to that branch.**
  If a task produced several logical changes, each gets its own branch — even when they touch
  the same files. Code that doesn't belong to the branch's topic moves to the later branch it
  relates to (see "Splitting work into branches" below).
- Subject: `<type>: <imperative summary>` — lowercase after the colon, no trailing period,
  ≲ 72 chars. Same types as branches (`feat`, `fix`, `chore`, `build`, `refactor`).
- Body (skip only for trivial changes): wrap at ~72 columns, explain **why** and what the user
  sees, not a file list. Either prose paragraphs or `- ` bullets. Mention Crashlytics issue ids
  or affected games when the change comes from a crash report.
- Never stage build outputs or release artifacts. Check `git status --short -uall` before committing
  new directories.
- Do not add `Co-Authored-By: Claude*` block in commit message 

## Landing on develop (rebase, then no-ff merge)

For each branch, in order:

```bash
git switch <branch>
git rebase develop                 # branch commit sits on the current develop tip
git switch develop
git merge --no-ff --no-edit <branch>
```

- The merge commit keeps git's default message `Merge branch '<branch>' into develop`, with no
  trailer — that is what every merge in the history looks like.
- Resulting shape (one commit per merge bubble):

  ```
  *   Merge branch 'fix/b' into develop
  |\
  | * fix: ...
  |/
  *   Merge branch 'feat/a' into develop
  ```

## Splitting work into branches

A branch holds **only** the code for its topic. Before committing, sort every hunk by topic:

1. List the topics (e.g. `feat/form_customitem`, `feat/form_scrolling`, `feat/choicegroup_touch`).
   Order them so a branch only depends on branches before it.
2. Each branch gets the hunks of its own topic. A hunk that isn't about this topic goes to the
   **later branch it relates to** — never ride along "because it's in the same file".
3. Shared files are split by hunk, not by file: e.g. two new APIs each add their own `-keep`
   line in `app/proguard-rules.pro` and their own section in `THIRD_PARTY_NOTICES.md` on their
   own branch; a test or fixture goes with the code it tests.
4. If a later topic rewrote code from an earlier one in the same hunk, the earlier branch gets
   the earlier version of that code (temporarily edit the file back, commit, then restore).
5. Every branch must build and pass tests on its own: after committing each one, run
   `./gradlew test assembleDebug` on that commit.

Staging part of a file without interactive `git add -p`: write the wanted hunks to a patch
(`git diff -- <file> > all.patch`, keep only the topic's hunks in `topic.patch`) and stage it
with `git apply --cached topic.patch`; check with `git diff --cached`.

- Splitting existing linear commits `C1..Cn` (already one topic each) into per-commit branches:

  ```bash
  git branch <name_i> <Ci>
  git rebase --onto develop <Ci>^ <name_i>
  git switch develop && git merge --no-ff --no-edit <name_i>
  ```

  Afterwards verify nothing changed: `git diff <old tip> develop` must be empty.
- Keep the feature branches after merging (the repo keeps them). Delete only temporary
  branches you created yourself.

## Guardrails

- Commit, merge or push only when the user asks; never push or force-push unless told to.
- Don't rewrite history that is already on `origin`.

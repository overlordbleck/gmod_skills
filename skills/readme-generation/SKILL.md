---
name: readme-generation
description: Generate or update a repository README from the current codebase. Run only when the user explicitly invokes $readme-generation for the current task; never run automatically during code generation or general documentation work.
---

# Garry's Mod Lua Addon: README

## Explicit invocation only

- Require the user to explicitly invoke `$readme-generation` for the current README task. A general coding request, a request to update documentation, or a plain README request without this invocation does not activate the skill. If needed, tell the user how to invoke it and leave README files unchanged.
- Authorization covers this README task and its follow-up corrections only. It does not persist into later implementation tasks, even if the skill remains loaded in the conversation.
- Without an explicit invocation, do not create, update, rename, delete, or reformat README files. Reading them for context is allowed.

## Generate from the repository

Describe only what the addon is and how it works in the current codebase, using present tense. Do not describe previous behavior, development history, or how the addon has changed. Exclude before/after comparisons, migration narratives, and statements such as "now supports," "previously," "no longer," "added," or "fixed" when they narrate a change. State the current behavior directly instead: "Supports X." When updating an existing README, replace historical wording with current facts and remove obsolete behavior. Keep change summaries in the response, outside the README, unless the user explicitly requests historical documentation.

1. Identify the target repository and read its applicable instructions. Use the existing root README when present; otherwise create `README.md` at the repository root. Honor a user-specified destination. If multiple repositories or README variants make the target ambiguous, ask which one to use before writing.
2. Read the existing README and inspect the code, dependency manifests, entry points, public API, configuration defaults, and operational commands. Establish facts before drafting; do not infer features from names alone.
3. Follow the [README content rules](../gmod-lua-addons/subskills/readme.md). For non-GMod repositories, omit GMod-specific subjects that do not apply and omit the API table's `Realm` column when the project has no realm concept. Follow explicit user requirements and target repository instructions before these default content limits.
4. Prepare the complete draft before editing the destination. Verify public signatures, return values, examples, dependencies, defaults, commands, and permissions against the current code. Report unresolved facts to the user instead of inventing content or writing placeholders.
5. Write the finished README, preserving unrelated user changes. Avoid incremental placeholder writes while inspecting or drafting. Keep changes scoped to the requested README; do not modify implementation code to match the documentation.
6. Review the final diff for accuracy, concision, broken relative links, unintended removals, and compliance with the content rules. Check that the README describes only the current addon and contains no change history or comparisons with previous behavior unless explicitly requested. Report the file created or updated and any verification limitations. Do not claim runtime testing unless it actually occurred.

Example invocation: `Use $readme-generation to generate or update this repository's README.md from the current code.`

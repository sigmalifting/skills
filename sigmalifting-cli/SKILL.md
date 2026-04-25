---
name: sigmalifting-cli
description: Use when generating, translating, validating, or reviewing SigmaLifting CLI JSON bundles, especially for program structure, block boundaries, weekly layout, and set-group modeling.
---

# SigmaLifting CLI

Use this skill when working with SigmaLifting CLI bundles, especially when turning a spreadsheet, manual, or public program into a `program-import` bundle.

## Core Boundaries

- Treat the CLI as a standalone JSON tool.
- Use the installed `sigmalifting-cli` executable as the contract and validation engine.
- Do not rely on Realm, Expo file APIs, or app services for CLI behavior.
- Prefer contract-valid JSON bundles over app-coupled shortcuts.
- Treat this skill folder as the modeling guide, not as the executable schema.

## CLI Discovery

When the installed CLI and this skill are available, inspect the tool before modeling:

1. run `sigmalifting-cli doctor --compact`
2. read this `SKILL.md` and any task-relevant files in `references/`
3. run `sigmalifting-cli help --compact`
4. run `sigmalifting-cli schema list --compact`
5. run `sigmalifting-cli template program-import --compact` or `template process-import --compact`

Program and process bundle results persist as local JSON by default. Use `sigmalifting-cli store path` to locate the store, and use `SIGMALIFTING_HOME` or `--store-root` when you need an explicit test or project-local store.

## Schema Contract

Do not treat this skill file as the schema.

This skill explains modeling judgment: block boundaries, weekly structure, set-group semantics, and common translation mistakes. The installed CLI is the schema source of truth.

Schema mentions in this skill are routing instructions, not schema documentation. They tell the agent which CLI commands to call before editing JSON.

Before writing or editing JSON, get the executable contract from the CLI:

1. `sigmalifting-cli schema list --compact`
2. `sigmalifting-cli schema show <kind> --compact`
3. `sigmalifting-cli template <kind> --compact`
4. `sigmalifting-cli validate <kind> --file <path|"-"> --compact`

Use the template to learn field shape, defaults, sentinel values, and nesting. Use `schema show` to check constraints. Use this skill to decide what the program should mean structurally.

If the skill and CLI appear to disagree, trust the CLI schema and report the skill as stale.

Do not invent fields that are not present in the CLI schema or template.

## Authoritative Sample Workflow

If the user provides an exported SigmaLifting JSON file, treat that export as the first source of truth.

Before generating or translating anything similar:

1. identify the bundle kind the file actually matches
2. run the standalone CLI against the file exactly as provided
3. validate it first
4. normalize it second
5. only then extract modeling rules from it

If an app-exported file cannot be consumed by the standalone CLI, treat that as a CLI problem to investigate before declaring the modeling wrong.

Do not skip this step and jump straight to reconstructing the program from memory, public spreadsheets, or a simplified interpretation.

## Block Semantics

Do not define a block by contiguous time alone.

A block is a maximal consecutive run of weeks that share the same structural skeleton.

The structural skeleton means:

- same training day layout
- same exercise identities on each day
- same set-group count and ordering per exercise
- same variable-parameter style per set group
- same dependency shape such as backoff or fatigue-drop relationships

What may vary inside a block:

- weekly set counts
- weekly reps
- weekly RPE values
- weekly percentages
- weekly notes

What should usually force a new block:

- a day appears, disappears, or changes role materially
- an exercise is added, removed, or replaced
- a set group appears or disappears
- a set group changes semantic role rather than just numeric values
- dependency structure changes
- the week only fits by abusing placeholder zero-sets across many exercises
- the phase intent clearly changes even if the schema could be compressed

Do not collapse an entire multi-week program into one block just because weekly arrays can encode it.

That is a schema-valid compression, but it is a modeling error under SigmaLifting semantics.

## Workflow

When modeling a program:

1. Inspect each week for structural sameness, not just duration.
2. Group only consecutive weeks that share the same skeleton.
3. Create one block per structural group.
4. Use weekly arrays only for parameter variation inside that block.
5. Preserve special-case instructions in notes if they are not truly machine-derived.
6. If unsure, prefer more blocks over fewer blocks.

## Warning Signs

Stop and re-evaluate the block split if you notice:

- many `0` set placeholders just to make weeks fit
- weeks that feel like distinct phases but are still in one block
- day layouts drifting across weeks
- exercise schemes changing identity from week to week

## References

- For detailed modeling guidance, read [references/program-modeling.md](references/program-modeling.md).
- For Candito-specific lessons extracted from an exported app bundle, read [references/candito-translation.md](references/candito-translation.md).
- For process execution and workout logging rules, read [references/process-logging.md](references/process-logging.md).

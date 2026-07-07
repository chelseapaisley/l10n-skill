# l10n-skill

A Claude skill that learns your localisation conventions and generates a personalised string writer you can point at any feature brief.

## What it does

This is a setup skill. Run it once to produce an `l10n-writer.md` skill tailored to your string files and team conventions. From then on, give the writer any feature brief — Jira, Figma frame, or plain description — and it produces MR/PR-ready strings in the correct format, file, and namespace.

**Setup flow:**
1. Provide your existing string files (MCP, upload, or paste)
2. The skill extracts all conventions automatically — naming, structure, placeholders, pluralisation, deprecation, translator notes
3. Two quick questions about team judgment calls that can't be inferred
4. Test against 2–3 real examples with a visible feedback loop
5. Review and confirm the full ruleset
6. Outputs your personalised `l10n-writer.md`

**Then day-to-day:** point `l10n-writer.md` at any Jira, Figma frame, or feature description → get a diff-ready set of strings across all your locale files.

## Files

```
l10n-skill/
├── README.md
├── SKILL.md                      ← the setup skill — install this
├── references/
│   └── file-analysis.md         ← extraction guide for string files
└── examples/
    └── example-output.md        ← sample brief → MR-ready strings
```

## Install

1. Clone or download this repo
2. Copy the `l10n-skill/` folder into your Claude skills directory
3. Reload skills
4. Say *"set up my localisation skill"* to start

## Output

**If GitHub or GitLab MCP is connected:** creates a branch, commits the updated locale files, raises a PR/MR with a generated description including what changed, which strings are new vs reused, and anything needing sign-off before merging.

**If no Git MCP is connected:** outputs the complete updated file contents (one block per locale file) plus a ready-to-paste PR/MR description the user can use when opening it manually.

## Accepted input formats (for the generated skill)

- Jira ticket (MCP fetch by ID, or paste the body)
- Figma frame (via MCP) or description of screen copy
- Plain description of what's being added
- Any combination

## What gets extracted automatically

- File structure and organisation
- Key naming convention and depth
- Placeholder/interpolation format
- Pluralisation convention
- Translator notes format (if used)
- Untranslated string handling (inferred from partially-translated locale files)
- Deprecation convention (inferred from file history or commented strings)

## The two upfront questions

Only two things can't be read from the files:

1. **Reuse vs new** — when a new feature needs a string similar to an existing one, does your team reuse the key or create a new context-specific one?
2. **Namespace gaps** — if a feature doesn't fit an existing namespace, what's the call?

Everything else comes from the files or emerges through the feedback loop.

## The feedback loop

When you correct a test draft, Claude proposes the general rule your correction implies, you confirm or refine it, and nothing gets baked in without your approval. At the end, the full ruleset is shown for review before the skill is generated.

## Re-running setup

Re-run when your file structure changes significantly, you add new locales, or the generated skill starts making wrong namespace calls consistently.

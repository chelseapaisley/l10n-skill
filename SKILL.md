---
name: l10n-skill
description: Use this skill when the user wants to set up a localisation string writer, build a tool for adding translation strings from a feature brief, or create a skill that takes a Jira, Figma frame, or feature description and outputs strings in the right format for a PR or MR. Trigger when the user says things like "set up my localisation skill", "build me an l10n tool", "create a skill for adding translation strings", or "I want Claude to write my i18n strings". This skill analyses existing string files, extracts all conventions automatically, asks two focused questions, tests against real examples, and outputs a personalised l10n-writer.md ready to install.
---

# Localisation Skill — Setup

This skill does one thing: learns your localisation conventions from your existing string files, confirms two team judgment calls it can't infer, tests against real examples with a visible feedback loop, and outputs a personalised `l10n-writer.md` that takes a feature brief and produces MR/PR-ready strings.

---

## Step 1 — Ingest the string files

Ask the user:

> "First I need to read your existing localisation files. How would you like to provide them?"
>
> **Option 1 — MCP**: If you have a Drive, repo, or docs MCP connected, point me at the folder or repo containing your string files.
>
> **Option 2 — File upload**: Upload the string files directly — any format is fine (JSON, YAML, `.strings`, `.arb`, `.po`, etc.). Include multiple locale files if you have them.
>
> **Option 3 — Paste**: Paste the contents of the key files directly.
>
> If you have files for multiple locales, include at least the source/base language and one or two others — the more complete the picture the better.

Once received, confirm: how many files, what format, which locales are present, and which appears to be the source language.

---

## Step 2 — Extract conventions

Analyse all provided files and extract everything as **rules**, not observations. Do not ask the user about anything that can be determined from the files.

Read `references/file-analysis.md` for the full extraction guide.

Produce an internal analysis summary covering all extracted conventions. Show the user a brief plain-language summary of what you found — e.g. "You're using nested JSON, keys follow a `feature.component.element` pattern, placeholders use `{{double_braces}}`, and you have 4 locales with EN as the source." Keep it short — this is confirmation, not a report.

---

## Step 3 — Two upfront questions

These are team judgment calls that can't be inferred from the files. Ask both together:

> "Two quick questions before we test:
>
> **1. Reusing existing strings**: When a new feature needs a string that's very similar to one that already exists — say, a 'Save' button that's already used elsewhere — does your team reuse the existing key, create a new one specific to this context, or does it depend on the situation?
>
> **2. When a feature doesn't fit an existing namespace**: If something new doesn't map neatly under any of your current namespaces, what's the call — create a new namespace, put it under the closest existing one, or flag it for a human to decide?"

Wait for both answers before proceeding.

---

## Step 4 — Test with real examples and iterate

Tell the user:

> "Now I'll test my understanding against some real examples. Give me 2–3 recent features or changes that needed new strings — anything works: a Jira ticket (ID if you have MCP, or paste the body), a Figma frame (via MCP or a description of what's on the screen), or just describe what was added. A mix is fine."

### Handling each input format

**Jira via MCP or pasted**: Extract what's changing, what the user sees, any copy specified in the AC or description.

**Figma via MCP**: Read the frame — extract visible text elements, labels, button copy, error states, placeholder text. These become the string values; the skill derives the keys.

**Figma without MCP**: Ask the user to describe what's on the screen or paste the relevant copy.

**Plain description**: Take it at face value. If copy isn't specified, draft reasonable placeholder values and flag them clearly as needing copywriter/PM sign-off.

### For each test example

1. Identify all strings needed — new UI copy, error messages, empty states, button labels, notifications, anything user-visible
2. Apply the reuse rule from Step 3 — check whether any needed strings match existing keys before creating new ones
3. Assign keys following the extracted naming convention
4. Place them in the correct file(s) and namespace(s), applying the namespace gap rule from Step 3 if needed
5. Format placeholders, pluralisation, and translator notes exactly as found in existing files
6. Output as a diff — which files change, what gets added, in what position

Show the user the draft diff.

### Feedback loop

After each draft, ask:

> "Does this look right? Tell me anything that's off — wrong key name, wrong file, wrong namespace, strings that should be reused not created, anything missing."

When the user gives feedback, do two things:

**1. Fix the draft.**

**2. Propose the rule it implies:**

> "I'll update my understanding: [proposed rule in plain language]. Does that sound right as a general rule, or was this a one-off?"

Wait for confirmation. Use their version if they refine it. Note one-offs without generalising them.

Keep a running **Rule Log** — shown to the user as it grows:

```
✓ Button labels that appear across multiple features use shared keys 
  under common.actions — don't create feature-specific duplicates
✓ Empty state strings go under [feature].empty_state, not [feature].messages
✓ New top-level namespaces are fine for major features — flag for review 
  for smaller ones
```

After all test examples, show the complete rule log:

> "Here are all the conventions I've learned — from your files and from our feedback. Review and edit anything before I generate your skill."

Let the user edit directly. Proceed only when they confirm.

---

## Step 5 — Generate the personalised skill

Output a complete, copy-pasteable `l10n-writer.md`. Tell the user:

> "Here's your localised string writer. Install it as a Claude skill — then point it at any Jira, Figma frame, or feature description and it'll produce MR-ready strings in your format."

The generated file must follow this structure, fully populated:

```markdown
---
name: l10n-writer
description: Write localisation strings for a new feature or change, ready 
for a PR or MR. Trigger when given a Jira, Figma frame, or feature description 
and asked to add, create, or write translation strings, i18n keys, or 
localisation files. Also trigger when asked "what strings does this need?" 
or "write the l10n for this."
---

# Localisation String Writer

Takes a feature brief in any format and produces MR-ready localisation strings 
in the correct format, file, and namespace.

## Accepted input formats
- Jira ticket (MCP fetch by ID, or pasted body)
- Figma frame (MCP) or description of screen copy
- Plain description of what's being added
- Any combination — e.g. a Jira with a Figma link

## File structure
[map of the string file organisation — which files exist, how they relate, 
which is the source language]

## Naming convention
[the key naming rules — structure, casing, depth, verbosity]

## Locales
[list of locales, source language, what to do for non-source locales]

## Placeholder format
[exact format with example — e.g. {{variable_name}}]

## Pluralisation
[convention used — framework syntax or separate keys, with example]

## Translator notes
[whether notes are used, format, when to include them]

## Untranslated string handling
[what goes in non-source locale files for new keys — empty, omitted, or 
source value as placeholder]

## Reuse rule
[when to reuse existing keys vs create new ones — from Step 3]

## Namespace gap rule
[what to do when a feature doesn't fit an existing namespace — from Step 3]

## Deprecation
[how removed strings are handled — delete, comment out, grace period]

## Output format

**If GitHub or GitLab MCP is connected:**
1. Create a branch named after the feature (infer from the Jira summary, Figma frame name, or description — e.g. `l10n/date-range-picker`)
2. Commit the updated locale files to that branch with a clear commit message
3. Raise a PR/MR with a generated description covering:
   - What feature this is for and a link to the Jira if one was provided
   - Which locales were updated
   - Which strings are new vs reused
   - Any copy flagged for copywriter/PM sign-off before merging
4. Tell the user the PR link and flag anything needing sign-off

**If no Git MCP is connected:**
Output the complete updated file contents for each locale file that changes — not a diff, since a diff requires the original in context to apply. One fenced code block per file, labelled with the file path.

Then output a ready-to-use PR/MR description the user can paste when opening it manually:

```
## What this does
[feature name and brief description]

## Changes
- [locale]: [N] strings added
- Reused: [list any reused keys]

## Before merging
[list any copy that needs sign-off, or "None — all copy confirmed in brief"]

Jira: [link if provided]
```

**Both paths:**
Flag any copy that was assumed (not explicitly stated in the brief) so the user knows to verify before merging. Flag any namespace judgment calls made so the user can confirm.

If Figma MCP is connected and the brief includes a Figma link, read the frame directly and extract copy from it before producing output.
```

Write the generated skill in first person. Omit any section that doesn't apply — if there are no translator notes in the files, don't mention them. If the user's team only uses one Git platform, name it specifically rather than saying "GitHub or GitLab".

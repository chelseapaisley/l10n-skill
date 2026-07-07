# String File Analysis Reference

This file is read during Step 2 to guide full extraction of localisation conventions from existing files. Extract everything as rules, not observations.

---

## File structure

- How many files are there and how are they organised? (one file per locale, one file per feature/namespace, nested folders)
- Are files flat or nested? If nested, what's the depth and logic?
- Is there a shared/common file separate from feature-specific files?
- What is the source/base language? (usually the most complete file, often `en`, `en-US`, or `en-GB`)
- Which locales are present? List them all.
- Are any locale files noticeably less complete than others? If so, what do the missing keys look like — absent entirely, empty string, or source value copied in? This is the untranslated string convention.

## Key naming convention

- What case is used? (`snake_case`, `camelCase`, `kebab-case`, `SCREAMING_SNAKE`)
- What is the structural pattern? (e.g. `feature.component.element`, `FEATURE_ELEMENT_STATE`, `feature/component/element`)
- How deep do keys typically go? What's the maximum nesting depth observed?
- Are keys verbose and descriptive (`settings.profile.save_button_label`) or terse (`profile.save`)?
- Are there prefixes for specific string types? (e.g. `error.`, `label.`, `action.`)
- Do button labels, error messages, and empty states follow distinct naming patterns?
- Is there a `common` or `shared` namespace for strings used across multiple features?

## Placeholder/interpolation format

- What format are dynamic values in? Check carefully — common formats:
  - `{{variable_name}}` (Handlebars/double brace)
  - `{variable_name}` (single brace)
  - `%s`, `%d`, `%@` (printf-style)
  - `%(variable_name)s` (Python-style)
  - `<variable>` (XML-style)
  - `$variable` (dollar-sign)
- Are variable names snake_case or camelCase inside placeholders?
- Are there examples of multiple placeholders in one string? Note the pattern.

## Pluralisation

- Are there any strings that handle count-based variations?
- If yes, which convention is used:
  - Framework plural keys (`_one`, `_other`, `_zero`, `_few`, `_many`) — ICU or similar
  - Separate explicit keys (`item_singular`, `item_plural`)
  - Inline syntax (`{count, plural, one {item} other {# items}}`)
- Note the exact format with a real example from the files.
- If no pluralisation exists, omit this section from the generated skill.

## Translator notes / comments

- Are there any comments in the string files? (JSON doesn't support comments natively — if present they may be in a `_comment` key or similar convention)
- YAML and `.po` files support comments natively — are they used?
- If notes exist, when do they appear? (always, only for ambiguous strings, only for strings with placeholders)
- What format are they in? Show an example.
- If no notes exist anywhere in the files, omit this section from the generated skill.

## Deprecation convention

- Are there any commented-out, marked, or archived strings?
- Is there a `deprecated` namespace, section, or file?
- Are removed strings deleted immediately or staged somehow?
- If no evidence of a deprecation convention exists, the default is immediate deletion — note this in the generated skill.

## String value conventions

- Are values sentence case, title case, or all lowercase?
- Are there trailing punctuation conventions? (periods on sentences, no periods on labels)
- Are there any strings that are clearly placeholders or TODOs? Note the format.
- Are emoji used in any strings?

---

## Reading partially-translated files

When comparing locale files to identify the untranslated string convention:

1. Find a locale file that's clearly less complete than the source
2. Pick 3–5 keys present in the source but absent or different in this locale
3. Determine: are they missing entirely, empty string `""`, or copied from the source language?
4. That's the untranslated string convention — embed it as a rule in the generated skill

If all locale files appear equally complete, note this and default to "omit from non-source files until translated."

---

## Output of this analysis

Produce rules in this format — concrete and actionable, not descriptive:

```
NAMING: Keys must follow feature.component.element pattern in snake_case, 
maximum 4 levels deep. Button labels end in _label, error messages end 
in _error, empty states end in _empty.

PLACEHOLDERS: Dynamic values use {{snake_case_name}} double-brace format.

PLURALISATION: Count-varying strings use _one and _other suffixes 
(e.g. item_count_one, item_count_other). No ICU syntax.

FILES: One JSON file per feature under /locales/[locale]/[feature].json. 
Shared strings go in /locales/[locale]/common.json.

UNTRANSLATED: New keys are omitted from non-source locale files until 
a translation is provided — never use empty string or source copy as placeholder.

TRANSLATOR NOTES: Not used — omit from generated skill.

DEPRECATION: No convention observed — default to immediate deletion.
```

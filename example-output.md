# Example: Feature Brief → PR

This shows the output skill in use, given a Jira ticket, with GitHub MCP connected.

---

## Input

**Jira ticket (pasted):**

```
Summary: Add date range picker to reporting dashboard

Description:
Users need to be able to filter the reporting dashboard by a custom date range.
The date picker should support selecting a start and end date.

Acceptance criteria:
- [ ] Date range picker appears above the report table
- [ ] User can select start date and end date independently
- [ ] Default range is last 30 days
- [ ] Selected range is shown as a label e.g. "15 Jan 2026 – 15 Feb 2026"
- [ ] Clicking "Apply" updates the report
- [ ] Clicking "Reset" returns to the default 30-day range
- [ ] If start date is after end date, show a validation error
```

---

## Output (GitHub MCP connected)

**Strings identified:** Labels, error state, dynamic range display
**Reuse check:** `common.actions.apply` and `common.actions.reset` already exist — reusing both, no new keys needed for those buttons.

Branch created: `l10n/date-range-picker`
Files updated: `locales/en/reporting.json`, `locales/fr/reporting.json`
PR raised: https://github.com/org/repo/pull/482

**PR description:**

---

> ## What this does
> Adds localisation strings for the date range picker on the reporting dashboard (PROJ-1084).
>
> ## Changes
> - `en`: 5 strings added under `reporting.date_range_picker`
> - `fr`: 5 strings added under `reporting.date_range_picker`
> - Reused: `common.actions.apply`, `common.actions.reset`
>
> ## Before merging
> - `reporting.date_range_picker.default_range_label` ("Last 30 days") — copy assumed from AC, confirm wording with PM
> - `reporting.date_range_picker.invalid_range_error` ("Start date must be before end date") — copy assumed from AC, confirm wording with copywriter
>
> Jira: https://yourorg.atlassian.net/browse/PROJ-1084

---

**Strings added:**

```json
// locales/en/reporting.json
"date_range_picker": {
  "label": "Date range",
  "start_date_label": "Start date",
  "end_date_label": "End date",
  "selected_range_label": "{{start_date}} – {{end_date}}",
  "default_range_label": "Last 30 days",
  "invalid_range_error": "Start date must be before end date"
}
```

```json
// locales/fr/reporting.json
"date_range_picker": {
  "label": "Plage de dates",
  "start_date_label": "Date de début",
  "end_date_label": "Date de fin",
  "selected_range_label": "{{start_date}} – {{end_date}}",
  "default_range_label": "30 derniers jours",
  "invalid_range_error": "La date de début doit être antérieure à la date de fin"
}
```

---

*Namespace note: Added under `reporting.date_range_picker` — fits existing `reporting` namespace cleanly, no new top-level namespace needed.*

---
name: odk-xlsform
description: Design, validate, deploy, and analyze ODK (Open Data Kit) XLSForms that are analysis-ready for Python/Pandas and SAS. Use whenever the user wants to build an ODK/XLSForm survey from a specification, generate a .xlsx form with proper survey/choices/settings sheets, add cascading selects or constraints, apply the snake_case + special-value house style, validate a form with xls2xform, run regression checks with PyXComparer, or push forms to and pull submissions from ODK Central. Trigger on mentions of XLSForm, ODK, ODK Central, xls2xform, pyxform, choice_filter/cascading selects, or survey-form authoring. Bundles a generator script, templates, an operational manual (manual.md), and a technical reference (reference.md).
license: See LICENSE / repository terms
---

# ODK XLSForm Authoring

Act as a **Master ODK Programmer**: translate natural-language survey requirements into production-ready, analysis-optimized XLSForm definitions. You are fluent in XLSForm syntax, ODK Central practices, snake_case naming, standardized special values, cascading selects, XPath logic, and constraint patterns — always producing forms that clean and analyze cleanly in Python/Pandas and SAS.

## Workflow

```
Design → Validate → Deploy → Analyze
```

1. **Design** — Turn the spec into survey/choices/settings rows. Apply the house style (below). Generate the `.xlsx` with `scripts/xlsform_generator.py`.
2. **Validate** — Stage 1: `xls2xform form.xlsx` (structural/syntax gate — use the CLI, not the pyxform package). Stage 2 (optional): [PyXComparer](https://github.com/joybindroo/PyXComparer) against a known-good baseline to catch regressions in `relevant`/`constraint` logic.
3. **Deploy** — Push the validated form to ODK Central via `pyODKmcp` or the ODK Central API.
4. **Analyze** — Use `pyODKmcp` to fetch submissions into a local SQLite DB, then query with SQL. Feed findings back into the form design.

## House Style (required for Python/SAS compatibility)

- **Variables**: `snake_case` with a module prefix — `demog_age`, `hh_size`, `agri_plot_area`. Groups: `grp_*`. Calculations: `calc_*`. Avoid leading digits; keep ≤ 64 chars.
- **Special values**: `-88` = Don't know, `-89` = Refused, `-90` = Not applicable, `99` = Other.
  - Numeric constraint pattern: `. >= 0 or . = -88 or . = -89 or . = -90`
- **Choices**: integer `value`s (never strings). `1` = Yes, `0` = No. ISO dates/times.
- **Cascading selects**: add filter columns to the `choices` sheet and use `choice_filter` (e.g. `state = ${state}`) in the `survey` sheet.

## Generating a form

`scripts/xlsform_generator.py` builds the `.xlsx`:

- `extract_template_metadata(template_path=None)` — reads the column headers from `templates/schema.json` so you know the exact schema before generating.
- `generate_xlsform(output_path, survey_data, choices_data, settings_data, schema_path=None)` — writes the workbook. `choices_data` accepts either the **advanced dict** form `{list_name: [{'name','label', ...filter_cols}, ...]}` (auto-derives filter columns for cascading selects) or a legacy flat list of rows.

Requires `pip install -r requirements.txt` (openpyxl, pandas, pyxform, pyodk) — `requirements.txt` sits in this skill directory, alongside `SKILL.md`.

⚠️ **Formula-stripping pitfall**: openpyxl/pandas can strip `${variable}` syntax out of formula cells on save. When editing forms with Python, use **XML patching** — unzip the `.xlsx`, raw-text-replace the broken formula back into `xl/worksheets/sheet1.xml`, rezip. Details in `reference.md` §5A.

## Bundled resources

| File | What it is |
|---|---|
| `manual.md` | Full operational manual — naming, special values, validation pipeline, reusable module templates (consent, household roster, geographic ID, assets, socio-economic, skills) |
| `reference.md` | Technical ODK reference — question types, operators/functions, cascading selects, audit logs, logic patterns, implementation pitfalls |
| `requirements.txt` | Python deps (openpyxl, pandas, pyxform, pyodk) |
| `scripts/xlsform_generator.py` | XLSForm generation engine |
| `templates/schema.json` | Column schema — source of truth for column order across survey, choices, settings, and entities sheets |
| `templates/odk_template_reference.md` | Reference content extracted from the original template — field types, appearances, relevance, constraints, translations, list lookups, and additional columns |
| `templates/field_types.json` | Supported field types |

**Read `manual.md` and `reference.md` before generating a nontrivial form** rather than working from this summary alone.

## ODK docs lookups

For any ODK syntax or behavior not covered in `manual.md` / `reference.md`, query the **`odk-docs` MCP server** (HTTP endpoint `https://odk-docs.mcp.kapa.ai`) if your agent has it configured — it indexes the official ODK documentation and community forum. Configure it however your CLI registers MCP servers (see the repo README). Prefer it over guessing or general web search, and still confirm anything it returns with `xls2xform`.

## Standing mandates

- Never output raw text tables as the deliverable form — always generate the `.xlsx`.
- Always validate with `xls2xform` before treating a form as done or committing it.
- Verify `relevant`/`constraint` expressions are valid ODK XPath.

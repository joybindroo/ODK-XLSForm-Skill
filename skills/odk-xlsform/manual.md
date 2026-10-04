# ODK Programming Skills

This document defines the operational skills and standards for AI agents acting as Master ODK Programmers.

## 1. Design & Construction

### Naming Conventions (House Style)
To ensure compatibility with Python (Pandas) and SAS, follow these rules:
- **Variable Names**: 
    - Use `snake_case` (lowercase with underscores).
    - Avoid starting names with numbers.
    - **Prefixing**: Use module-based prefixes to group variables (e.g., `demog_age`, `demog_gender`, `hh_size`, `agri_plot_area`).
    - **Length**: Keep names descriptive but concise (recommended $\le 64$ characters).
- **Group Names**: Prefix with `grp_`. Example: `grp_household_info`.
- **Calculation Fields**: Prefix with `calc_`. Example: `calc_total_income`.

### Special Value Conventions
Standardize "missing" or "non-applicable" values to simplify data cleaning:
- `-88`: Don't know
- `-89`: Refused
- `-90`: Not applicable
- `99`: Other (specify)
- **Constraint Pattern**: For numeric fields allowing special values: `. >= 0 or . = -88 or . = -89 or . = -90`

### Technical Implementation
- **Cascading Selects**: Implement hierarchies (e.g., District -> Block -> Village) by adding filter columns to the `choices` sheet and using `choice_filter` in the `survey` sheet.
- **Formula Preservation**: When using Python to edit forms, use **XML Patching** (raw text replacement in `xl/worksheets/sheet1.xml`) to prevent libraries like `openpyxl` from stripping `${variable}` syntax in `concat()` or complex calculations.
- **Bilingual Media Columns**: In multilingual forms (e.g. `label::English (en)` / `label::Hindi (hi)`), pyxform treats `image` as a translatable column and warns for each language lacking `image::<lang>`. The warning is cosmetic — the same image renders in every language — but the clean fix is to declare `image::English (en)` and `image::Hindi (hi)` columns and set both to the same filename. See `reference.md` §5D.
- **Data Analysis Alignment**: 
    - Always use integers for `value` in the `choices` sheet.
    - Use standard ISO formats for Date/Time.
    - Use `1` for Yes and `0` for No (Boolean).

### ODK docs lookups (when you can't figure it out)
If you (the agent) cannot confidently determine ODK/XLSForm syntax, behavior, or a community pattern from this manual, `reference.md`, or your own training knowledge, consult the **`odk-docs` MCP server** at `https://odk-docs.mcp.kapa.ai` (HTTP transport; configure in your CLI — see the repo README). It indexes the official ODK documentation and the ODK forum. Ask **specific, narrow questions** — e.g. "How does the audit `audit-file` attribute work in XLSForm?" or "What are the valid appearance values for `select_multiple`?". Always confirm anything it returns by validating with `xls2xform`.

## 2. Validation Pipeline
The validation process must follow this strict sequence:

### Step 1: Structural Validation (XLSForm-Validation-Skills / xls2xform CLI)
The primary gate for syntax and structural correctness.
- **Action**: Run `xls2xform your_form.xlsx` or use the `check_xlsform.py` script from the SwissTPH toolkit.
- **Requirement**: Ensure the form is syntactically valid and can be converted to XML. Do NOT rely solely on the `pyxform` Python package for script-based validation.

### Step 2: Regression Audit (PyXComparer)
Once structurally valid, use [PyXComparer](https://github.com/joybindroo/PyXComparer) to detect breaking changes.
- **Action**: Compare the current version against a known-good baseline to isolate deltas.
- **QA Gate**: Review changes in `relevant` and `constraint` columns to ensure no regressions in survey logic.
- **Documentation**: Export the form to YAML/JSON to provide a machine-readable data dictionary for analysts.

### Step 3: Deployment
Deploy the validated and audited form to ODK Central via `pyODKmcp` or the API.

## 3. Generation Workflow
1. **Persona**: Initialize as "Master ODK Programmer".
2. **Schema**: Define the form structure using `templates/schema.json` (in this skill).
3. **Generation**: Use `scripts/xlsform_generator.py` to create the `.xlsx` file.
4. **Validation**: Run `xls2xform` CLI $\rightarrow$ PyXComparer.
5. **Audit**: Log all changes in the project's audit trail.

## 4. Analysis & Monitoring (pyODKmcp)
`pyODKmcp` is an MCP Server that enables AI agents to interact directly with ODK Central.
- **Discovery**: Use `list_projects()` and `list_forms()` to map the environment.
- **Ingestion**: Use `get_data()` to pull submissions and persist them into a local SQLite database.
- **Analysis Loop**: "Fetch $\rightarrow$ Store $\rightarrow$ Analyze" using a Database MCP server (like `pyMCP`) to run SQL queries on the ingested data.
- **Feedback**: Use analysis results to identify patterns or errors and suggest improvements to the XLSForm design.

## 5. Reusable Modules (Templates)
Use these standardized blocks to ensure consistency:
- **Header & Metadata**: `start`, `end`, `deviceid`, `phonenumber`, `username`, and `calc_duration`.
- **Informed Consent**: `consent_note` -> `consent_given` (select_one yesno) -> `consent_fail` (note).
- **Household Roster**: `hh_size` -> `begin_repeat` (`grp_hh_roster`) -> `mem_name`, `mem_gender`, `mem_age`, `mem_relation`.
- **Geographic ID**: Cascading selects for `loc_state` -> `loc_district` -> `loc_village` and `loc_gps`.
- **Asset Checklist**: `select_multiple assets` $\rightarrow$ `calc_asset_count` using `count-selected()`.
- **Socio-Economic Profile**: Standardized blocks for Education (`edu_highest_level`, `edu_institution`, etc.) and Employment (`emp_status`, `occ_category`, etc.).
- **Technical Skills**: `select_multiple tech_skills` -> Dynamic repeat group `grp_skill_proficiency` to capture proficiency levels and years of experience.

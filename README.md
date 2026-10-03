# ODK XLSForm Skill

An agent-agnostic [Agent Skill](https://opencode.ai/docs/skills/) that turns any capable coding-agent CLI into a **Master ODK Programmer** — designing, validating, deploying, and analyzing [ODK](https://getodk.org/) XLSForms that are analysis-ready for **Python (Pandas)** and **SAS**.

The repo follows the portable `SKILL.md` convention (used by Claude Code, OpenCode, and other agents) — no vendor-specific plugin/marketplace files.

## What you get

The `odk-xlsform` skill packages an end-to-end workflow:

```
Design → Validate → Deploy → Analyze
```

- **Design** — generate valid `.xlsx` forms from a spec, with the house style baked in (snake_case naming, integer choices, standardized special values, cascading selects).
- **Validate** — structural gate via the `xls2xform` CLI, plus optional regression checks with [PyXComparer](https://github.com/joybindroo/PyXComparer).
- **Deploy** — push to ODK Central via `pyODKmcp` or the API.
- **Analyze** — pull submissions into SQLite and query with SQL, feeding results back into the form design.

## Repository structure

```
skills/
  odk-xlsform/
    SKILL.md                      # skill entry point (workflow, house style, mandates)
    manual.md                     # operational manual (conventions, modules, pipeline)
    reference.md                  # technical ODK reference (syntax, XPath, pitfalls)
    scripts/xlsform_generator.py  # XLSForm generation engine
    templates/                    # base .xlsx template + schema/field-type JSON
    requirements.txt              # openpyxl, pandas, pyxform, pyodk
```

## Using the skill

Each agent discovers `SKILL.md` skills from its own directories. Point your agent at this skill by cloning it into a discovery path (or symlinking `skills/odk-xlsform` there):

**Claude Code** — `~/.claude/skills/odk-xlsform/` (global) or `.claude/skills/odk-xlsform/` (per-project)

**OpenCode** — `~/.config/opencode/skills/odk-xlsform/` (global) or `.opencode/skills/odk-xlsform/`, `.claude/skills/odk-xlsform/`, or `.agents/skills/odk-xlsform/` (per-project)

**Other agents** — drop `odk-xlsform/` into whatever directory your CLI scans for `SKILL.md` skills.

Example (Claude Code, global):

```bash
git clone https://github.com/joybindroo/ODK-Form-Skills /tmp/odk
cp -r /tmp/odk/skills/odk-xlsform ~/.claude/skills/
```

Once discovered, the skill activates automatically when you ask to build, validate, or analyze an ODK/XLSForm survey.

## ODK docs MCP (optional but recommended)

The skill can consult the **`odk-docs`** MCP server for authoritative ODK documentation and community-forum knowledge:

- Endpoint: `https://odk-docs.mcp.kapa.ai` (HTTP transport)

Register it however your agent configures MCP servers:

**Claude Code**
```bash
claude mcp add --transport http odk-docs https://odk-docs.mcp.kapa.ai
```

**OpenCode** — in `opencode.json`:
```json
{
  "mcp": {
    "odk-docs": { "type": "remote", "url": "https://odk-docs.mcp.kapa.ai", "enabled": true }
  }
}
```

Any MCP-compatible agent can connect to the same endpoint directly.

## Requirements

```bash
pip install -r skills/odk-xlsform/requirements.txt   # openpyxl, pandas, pyxform, pyodk
```

- `xls2xform` (ships with `pyxform`) for validation — use the **CLI**, not the Python package.
- Optional: [PyXComparer](https://github.com/joybindroo/PyXComparer) for regression testing; `pyODKmcp` for ODK Central integration.

## Standards at a glance

| | |
|---|---|
| **Naming** | `snake_case` with module prefix (`demog_*`, `hh_*`, `agri_*`); `grp_*`, `calc_*` |
| **Special values** | `-88` don't know, `-89` refused, `-90` N/A, `99` other |
| **Choices** | integer values; `1`/`0` for yes/no; cascading via `choice_filter` |
| **Validation** | `xls2xform` CLI → PyXComparer regression |

See `skills/odk-xlsform/manual.md` and `skills/odk-xlsform/reference.md` for the full detail.

# ODK Technical Reference

This library provides a quick-reference for ODK XLSForm syntax, operators, and logic patterns based on official ODK documentation.

## 1. Core Question Types
| Type | Description | Appearance/Notes |
| :--- | :--- | :--- |
| `text` | Open-ended text | `multiline` for long text |
| `integer` | Whole numbers | Use constraints for range validation |
| `decimal` | Floating point numbers | Use constraints for range validation |
| `select_one [list]` | Single choice | `horizontal`, `minimal` |
| `select_multiple [list]` | Multiple choice | `horizontal` |
| `geopoint` | GPS coordinates | Captures lat/long/alt |
| `calculate` | Hidden calculation | Used for logic and data transformation |
| `note` | Display text/info | Used for instructions or calculated results |
| `begin_repeat` / `end_repeat` | Roster/Loop | Use `repeat_count` for fixed-size loops |

## 2. Operators and Functions
### Math Operators
- `+`, `-`, `*` : Standard arithmetic.
- `div` : Division (e.g., `${a} div ${b}`).
- `mod` : Modulo (remainder).

### Logic Operators
- `and`, `or`, `not` : Boolean logic.
- `=` , `!=`, `<`, `>`, `<=`, `>=` : Comparison.

### Key Functions
- `coalesce(val1, val2, ...)` : Returns the first non-empty value.
- `if(condition, true_val, false_val)` : Conditional logic.
- `selected(list, choice)` : Checks if a specific choice is selected in a `select_multiple`.
- `count()` : Counts number of selections in a `select_multiple`.
- `indexed-repeat(name, index, repeat_group)` : Retrieves a value from a specific repeat instance. Use this to pull data from a previous roster iteration into a calculation or note.
- `pulldata('csvname', 'column', 'key')` : Pulls data from an external CSV file uploaded to the device. Essential for pre-loading beneficiary lists or site metadata.
- `once(expression)` : Evaluates the expression only once and stores the result.

## 3. Advanced ODK Features

### A. Automated Data Capture
- **Automatic GPS Capture**: Use `geopoint` with the `device` trigger, or set a `calculate` field with `once()` to capture location automatically upon form start.
- **Background Audio Recording**: Use the `audio` question type. For background recording, ensure the device settings allow the app to record while the screen is off or in the background.

### B. Form Audit Logs (Enumerator Behavior Tracking)
To track how a form is filled, how long questions take, and when answers are changed, add a row of `type: audit` and `name: audit` to the survey sheet.
- **Basic Audit**: `type: audit`, `name: audit`
- **Change Tracking**: Add `track-changes=true` to the `parameters` column to record old and new values when an answer is edited.
- **Change Reasons**: Add `track-changes-reasons=on-form-edit` to force enumerators to provide a reason before saving changes.
- **User Identification**: Add `identify-user=true` to require the enumerator to identify themselves before starting/editing.
- **Location Tracking**: Add `location-priority`, `location-min-interval`, and `location-max-age` to the `parameters` column to geotag audit events.
- **Output**: ODK Central exports these as an `audit.csv` file per submission.

### C. Cascading Selects (Choice Filtering)
- **Implementation**: Use the `choice_filter` column in the `survey` sheet.
- **Logic**: `choice_filter = "category_column = ${parent_question}"`.
- **Custom Columns**: You can add custom columns to the `choices` sheet (e.g., `category`, `region_id`, `status`). These columns are not displayed to the user but can be used in `choice_filter` expressions to dynamically filter the list of available options based on previous answers.

## 4. Logic Patterns
### Relevance (`relevant` column)
- **Simple**: `${age} > 18`
- **Choice-based**: `selected(${gender}, 'female')`
- **Complex**: `${age} > 18 and selected(${employment}, 'employed')`

### Constraints (`constraint` column)
- **Range**: `. >= 0 and . <= 120`
- **With Special Values**: `(. >= 0 and . <= 120) or . = -88 or . = -89 or . = -90`
- **Cross-field**: `. <= ${parent_age}`

### Calculations (`calculation` column)
- **Summing**: `${income1} + ${income2} + ${income3}`
- **Conditional**: `if(${age} <<  18, 'Minor', 'Adult')`


## 5. Implementation Pitfalls & Advanced Fixes

### A. Formula Stripping in Python (openpyxl/pandas)
When using Python libraries like pandas or openpyxl to write or edit XLSForms, formulas containing ${variable} syntax (e.g., concat(${district}, '-', ${block})) are often stripped or corrupted during the save process. This results in invalid XForms (e.g., concat(, '-', , '-')).

**Solution: XML Patching**
Since .xlsx files are zipped XMLs, the most reliable way to preserve ODK formulas is to perform a raw text replacement on the underlying XML:
1. Unzip the .xlsx file.
2. Locate xl/worksheets/sheet1.xml.
3. Perform a string replacement of the 'broken' formula with the intended ${} syntax.
4. Re-zip the file.

### B. Internalizing External CSVs
While pulldata() is powerful, moving small-to-medium lookup lists (like District/Block/Village hierarchies) directly into the choices sheet improves form portability and reduces deployment errors.

**Pattern for Cascading Hierarchies:**
1. **Choices Sheet**: Add custom columns for the hierarchy (e.g., district, block, hsc).
2. **Survey Sheet**: Use choice_filter to link levels:
   - Block filter: district = ${district_question}
   - HSC filter: block = ${block_question}
   - Village filter: hsc = ${hsc_question}

### C. Standardizing List Names
To avoid 'List name not in choices sheet' errors during validation, ensure a strict naming convention for lists (e.g., always use district_list instead of mixing district and district_list). Use automated scripts to sanitize type columns across multiple forms to ensure consistency.

### D. Bilingual Forms with an `image` Column (pyxform Warning)
pyxform treats `image` as a translatable column: once a form declares language-qualified labels (e.g. `label::English (en)` / `label::Hindi (hi)`), it expects the media column to be language-tagged too and warns for each language that lacks `image::<lang>`.

| Form | Media columns | Warning? |
| :--- | :--- | :--- |
| Bilingual | plain `image` | Yes — one per language lacking `image::<lang>` |
| Bilingual | none | No |
| Single-language | plain `image` | No |
| Bilingual | `image::English (en)` + `image::Hindi (hi)` | No |

The warning is purely cosmetic — the same image card shows in both languages and all `jr://images/...` references bind correctly.

**Fix**: in any form using multiple languages with media image display, rename the schema column to `image::English (en)` and `image::Hindi (hi)` (one per declared language) and set both to the same filename.

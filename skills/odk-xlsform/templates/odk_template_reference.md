# ODK XLSForm Template Reference

Reference content extracted from the original `odk_template.xlsx` informational sheets.

## Getting started with XLSForms

- The XLSForm standard lets you create powerful forms for the ODK ecosystem.
- A form is defined using 4 sheets:
  - The **survey** sheet defines the questions and logic for your form. It is required.
  - The **choices** sheet defines choices to use in select questions.
  - The **settings** sheet defines form metadata and configuration.
  - The **entities** sheet is used to create entities from form values for use in longitudinal workflows, case management, and more.
- The template sheets contain the most common columns. See the Translations and Additional columns sections for other columns.
- Hover over column names to see a note about how they can be used.
- Columns colored in green on the survey and choices sheets can be translated.
- Use File > Make a copy to make a copy of this template that you can edit.
- Using a consistent column order makes it easier to copy and paste between different form definitions.
- Consider hiding columns you don't need instead of deleting them.

### Resources

- ODK form design docs
- XLSForm tutorial
- Forum for support
- Have ideas for improving this template? Share them here!

### Credits

- Styling ideas from yxf script
- Inspiration from CartONG's XLSform cheat sheet

### Changelog

| Date | Change |
|------|--------|
| 2025-11-21 | Add hidden type, big-image additional column |
| 2024-12-05 | Add counter appearance |
| 2024-07-08 | Add masked appearance |
| 2024-07-01 | Add table-list, hidden-answer and printer appearances |
| 2024-02-13 | no-calendar appearance does not work for Enketo |
| 2024-01-29 | Add entity_id and update_if to "Additional columns", add table-list to appearances, add grid theme reference |
| 2023-11-02 | Add decimal and text numbers for thousands-sep appearance; add filters on types, appearances and additional columns sheets (thanks @wroos!) |
| 2023-10-24 | Add Make a copy tip to readme |
| 2023-10-16 | v2023.1 release |

## Types

The survey sheet's `type` column determines how the user will be prompted or what will be computed by the form.
See the Appearances section to learn how to modify how a question is displayed to users.
The `parameters` column is sometimes used to configure aspects of a question type that are not appearance-related.

| Field type | Description | Collect mobile | Enketo web | Parameters |
|------------|-------------|:--------------:|:----------:|------------|
| text | Prompt the user for a text response | Yes | Yes | |
| integer | Prompt the user for an integer response | Yes | Yes | |
| decimal | Prompt the user for a decimal response | Yes | Yes | |
| note | Show the user some text | Yes | Yes | |
| calculate | Record the result of an expression specified in the calculation column | Yes | Yes | |
| select_one list_name | Prompt the user to select one choice out of a list | Yes | Yes | randomize, seed |
| select_multiple list_name | Prompt the user to select multiple choices out of a list | Yes | Yes | randomize, seed |
| select_one_from_file file.extension | Prompt the user to select one choice out of a list of choices from a file | Yes | Yes | randomize, seed, value, label |
| select_multiple_from_file file.extension | Prompt the user to select multiple choices out of a list of choices from a file | Yes | Yes | randomize, seed, value, label |
| begin_repeat | Indicate the start of a group of questions that are repeated | Yes | Yes | |
| end_repeat | Indicate the end of a group of questions that are repeated | Yes | Yes | |
| begin_group | Indicate the start of a group of questions | Yes | Yes | |
| end_group | Indicate the end of a group of questions | Yes | Yes | |
| geopoint | Prompt the user to record a single point (a location) | Yes | Yes | capture-accuracy, warning-accuracy, allow-mock-accuracy |
| geotrace | Prompt the user to record a line of points | Yes | Yes | |
| geoshape | Prompt the user to record a polygon of points (first and last point are identical) | Yes | Yes | |
| start-geopoint | Record the highest-accuracy point within 20 seconds of creating the blank filled form | Yes | Yes | |
| range | Prompt the user to select a value in an integer or decimal range defined in the parameters column | Yes | Yes | start, end, step |
| image | Prompt the user for an image | Yes | Yes | max-pixels |
| barcode | Prompt the user to scan a barcode | Yes | No | |
| audio | Prompt the user to record or select audio | Yes | Yes | quality (normal, low, voice-only, external; defaults to normal) |
| background-audio | Record audio in the background while filling out the form | Yes | No | quality (normal, low, voice-only) |
| video | Prompt the user to record or select a video | Yes | Yes | |
| file | Prompt the user to attach a file | Yes | Yes | |
| date | Prompt the user for a date response | Yes | Yes | |
| time | Prompt the user for a time response | Yes | Yes | |
| datetime | Prompt the user for a date and time response | Yes | Yes | |
| rank | Prompt the user to rank a list of choices | Yes | Yes | |
| csv-external | Attach a CSV to the form for querying. Specify its name without extension in the name column | Yes | Yes | |
| acknowledge | Prompt the user to acknowledge a statement | Yes | Yes | |
| start | Record the time when the blank filled form is created | Yes | Yes | |
| end | Record the time the last time that the filled form is finalized | Yes | Yes | |
| today | Record the date when the blank filled form is created | Yes | Yes | |
| deviceid | Record the install's unique ID (reset on uninstall or cache clear) | Yes | Yes | |
| username | Record the username | Yes | Yes | |
| phonenumber | Record the phone number stored in app settings | Yes | No | |
| email | Record the email stored in app settings | Yes | No | |
| hidden | Hidden field that can record the result of a calculation, have a default value, or have no value at all. | Yes | Yes | |
| audit | Log user's actions while filling out the form as an attached CSV | Yes | No | location-priority, location-min-interval, location-max-age, track-changes, track-changes-reasons, identify-user |

## Appearances

Appearances configure how a question will be displayed to the user on their device.
Most appearances for the same question type can be combined by using a space-separated list.

| Appearance name | Description | Type(s) used with | Collect mobile | Enketo web |
|-----------------|:------------|:-----------------:|:--------------:|:----------:|
| numbers | Restrict input to numeric symbols but save the value as text | text | Yes | depends on browser |
| multiline | Show a text area that is multiple lines tall | text | No (text boxes grow vertically as needed) | Yes |
| url | Show a button to launch the website represented by this field's value | text | Yes | Yes |
| ex: | Add the Android app ID of a custom application after the ex: prefix to launch that application | text integer decimal image audio video file | Yes | No |
| thousands-sep | Automatically add locale-dependent thousands separators on screen but not in submissions | integer, decimal, text numbers | Yes | Yes |
| bearing | Prompt the user to capture the device-reported compass direction | decimal | Yes | No |
| vertical | Show a vertical range slider instead of the default horizontal slider | range | Yes | Yes |
| no-ticks | Hide tick marks for a range slider (can combine with vertical) | range | Yes | Yes |
| picker | Show values in a range as a list of options that can be picked | range | Yes | Yes |
| rating | Show values in a range as star ratings | range | Yes | Yes |
| new | Only show the option to capture new media, not to select existing | image audio video | Yes | depends on browser |
| new-front | Only show the option to capture a new picture from front (selfie) camera | image | Yes | depends on browser |
| draw | Prompt the user to draw an image | image | Yes | Yes |
| annotate | Prompt the user to annotate (draw on) an image | image | Yes | Yes |
| signature | Prompt the user to sign their signature | image | Yes | Yes |
| no-calendar | Show date picker as spinners rather than a calendar | date datetime | Yes | No |
| month-year | Show date picker for month and year only | date | Yes | depends on browser |
| year | Show date picker for year only | date | Yes | some desktop browsers |
| ethiopian | Show date picker using the Ethiopian calendar | date | Yes | No |
| coptic | Show date picker using the Coptic calendar | date | Yes | No |
| islamic | Show date picker using the Islamic calendar | date | Yes | No |
| bikram-sambat | Show date picker using the Bikram Sambat (Nepali) calendar | date | Yes | No |
| myanmar | Show date picker using the Myanmar calendar | date | Yes | No |
| persian | Show date picker using the Persian calendar | date | Yes | No |
| placement-map | Show a map that the user can manually place and adjust a location on | geopoint | Yes | Yes |
| maps | Show a map that the user can capture device location on (but not manually adjust) | geopoint | Yes | Yes |
| hide-input | Show a larger map and hide geo input fields by default | geopoint geotrace geoshape | N/A | Yes |
| minimal | Show a text prompt that when tapped shows all choices | all selects | Yes | Yes |
| search | Allow the user to search the list of available choices | all selects | Yes | Yes (select one only) |
| quick | Advance to the next screen immediately after a choice is selected | select_one select_one_from_file | Yes | No |
| columns-pack | Show available choices with as may as possible on one line | all selects | Yes | Yes |
| columns | Show available choices in 2, 3, 4 or 5 columns depending on screen size | all selects | Yes | Yes |
| columns-n | Show available choices in the specified number (n) of columns | all selects | Yes | Yes |
| no-buttons | Show available choices without radio buttons / check boxes | all selects | Yes | Yes |
| image-map | Show available choices mapped to the svg file specified in the image column | all selects | Yes | Yes |
| likert | Show available choices horizontally as a likert scale | select_one select_one_from_file | Yes | Yes |
| map | Show available choices on a map using each choice's geometry column | select_one select_one_from_file | Yes | No |
| field-list | Show all questions in the field list on the same screen | begin_group begin_repeat | Yes | Yes (only applies in pages mode) |
| label | Show only option labels to define the top row of a select grid | all selects | Yes | Yes |
| list-nolabel | Show only radio buttons or checkboxes to define the inside of a select grid | all selects | Yes | Yes |
| list | Show horizontal radio buttons or checkboxes with their labels | all selects | Yes | Yes |
| table-list | Shortcut for building a grid of list and list-nolabel questions with a title | begin_group | Yes | Yes |
| hidden-answer | Hides the scanned barcode value | barcode | Yes | No |
| printer | Send the value of the question to the printer app for preview and printing | text | Yes | No |
| masked | Show asterisks (*) instead of what the user is entering | text | Yes | No |
| counter | Show buttons for incrementing and decrementing | integer | Yes | No |

## Relevance

Relevance determines whether a question will be displayed to a user or not. It lets you define branching or skip logic in your forms.

- Apply relevance to groups or repeats to skip or show multiple questions at once.
- You can use relevance on a note to provide additional guidance or to confirm a value that could possibly be out of range.
- You can meet almost any requirement with available functions.

| Example relevance expression | Description |
|------------------------------|-------------|
| `${q1} != ''` | This question will appear if q1 was not blank |
| `${age} < 18` | This question will only appear if previously-entered age was less than 18 |
| `${random_value} < 0.5` | If ${random_value} is a calculate with calculation once(random()), this question will be shown half the times that this form is launched |
| `(${computed_months} >= 6 and ${computed_months} < 24) or (${months} >= 6 and ${months} < 24)` | Conditions can be combined using and, or |
| `selected(${q1}, 'nurse')` | This question will only appear if 'nurse' was selected in q1 |
| `selected(${q1}, -88) or selected(${q2}, -88) or selected(${q3}, -88)` | This question will appear if -88 was selected in any of q1, q2 and q3 |
| `${q1} < 12.5 or selected(${q2}, 'y')` | This question will appear if q1's value was less than 12.5 or if 'y' was selected in q2 |
| `not(selected(${q2}, 'b'))` | This question will appear if 'b' was not selected in q2 |
| `string-length(${q1}) > 3` | This question will appear if the answer to q1 had more than 3 characters |
| `false()` | This question will never appear. This can be useful when creating a form that is intended to be customized for different environments. Questions or sections that are optional can be marked as non-relevant in the template. |

## Constraints

Constraints limit the answers allowed in a field.

- Constraints are not evaluated if the answer is blank. To require answers, make the question required.
- You can meet almost any requirement with available functions.

| Example constraint expression | Description |
|-------------------------------|-------------|
| `. <= 10` | Answer must be a number less or equal to 10 |
| `. > 10.51 and . < 18.39` | Answer must be a number between 10.51 and 18.39, excluding those values |
| `string-length(.) > 5` | Answer must be longer than 5 characters |
| `. >= today()` | Answer must be a date today or later |
| `. >= date('2015-08-01')` | Answer must be a date August 1st 2015 or later |
| `count-selected(.) <= 3` | Answer to this multi select question can only include 0, 1, 2 or 3 choices but no more |
| `not(selected(., 'none') and count-selected(.) > 1)` | Answer to this multi select question may not include both 'none' and other selections |
| `if(selected(., 'none'), count-selected(.) = 1, true())` | Another way of expressing the same concept as above: if the 'none' choice is selected, the total number of choices selected must be 1. Otherwise, any number of selections is allowed. Replace true() with another expression to specify a requirement when 'none' is not selected |
| `if(selected(., 'none'), count-selected(.) = 1, count-selected(.) = 2)` | Answer to this multi select question can either be 'none' or exactly two choices not including 'none' |
| `regex(., '^[A-Za-z]{0,6}$')` | Answer to this text question may have up to 6 letters |
| `regex(.,'^[A-Z]{3}+_+[A-Z]{3}+_+[0-9]{4}+_+[0-9]{3,4}$')` | Answer must respect a specific structure (for example this one allows the following : CAR_PRC_2015_048) |
| `regex(., '^[A-Za-z0-9._%+-]+@[A-Za-z0-9.-]+\.[a-zA-Z]{2,4}$')` | Answer to this text question must have an email address structure (note: this is not exact but filters out obviously-wrong inputs) |
| `regex(.,'^\(?[0-9]{3}\)?-?[0-9]{3}-?[0-9]{4}$')` | Answer to this text question must have a North American phone number structure |
| `. = 9999 or (selected(${q1}, 'yes') and . <= 0) or (selected(${q1}, 'no') and . > 0)` | The answer 9999 is always accepted to represent 'unknown'. Otherwise, this question's answer depends on ${q1}'s answer: if ${q1} was 'yes', then this answer must be a negative number. If ${q1} was 'no', this answer must be a positive number. |

## Translations

XLSForms can be translated into multiple languages.

- The text on navigation buttons and other system elements are translated as part of the application that shows your form, not as part of the form definition.
- Form titles can't be translated.
- We recommend that you design your form in one language, test it extensively, and then translate it.
- You can specify a default language in the settings sheet.
- There is no fallback language. Specify a translation for every column used in the form. Otherwise, a dash (-) will be shown for text that is not translated.
- If you use the choices sheet, make sure that all of its text and media columns have translations for all languages.

To add new language columns:

1. Duplicate all columns that have user-facing text or media.
2. Add the language name and the language code after a `::` separator for every column with text or media.
3. Find language codes.
4. The language name is what data collectors will see. We generally recommend using the language name in the language itself.

Example columns:

```
label::Español (es)
hint::Español (es)
```

Example with English and Spanish translations:

**survey sheet**

| type | name | label::English (en) | label::Español (es) | hint::English (en) | hint::Español (es) | image::English (en) | image::Español (es) |
|------|------|---------------------|---------------------|--------------------|--------------------|---------------------|---------------------|

**choices sheet**

| list_name | name | label::English (en) | label::Español (es) | image::English (en) | image::Español (es) |
|-----------|------|---------------------|---------------------|--------------------|--------------------|---------------------|---------------------|

## List lookups

You can look up values from lists specified in the choices sheet, entity lists, attached CSVs, attached geoJSON files and attached XML files by using the `instance` function.

- Choice filters, used to filter lists of items for use in selects, are the same as filter expressions.
- These queries use a subset of the XPath language. `${}` expressions get translated to XPath to access fields in the form.

```
instance('list_name')/root/item[filter expression]/desired_property
```

| Token | Description |
|-------|-------------|
| list_name | The name of the list to get value(s) from |
| filter expression | An expression for filtering the items in the list. You can filter the list down to one single item or multiple. Filter expressions can use functions and any other logic. |
| property | The property you want to access for items that match the filter |

The most common type of query looks up a single item based on one property and gives back another property's value:

```
instance('list_name')/root/item[match_property = ${q1}]/desired_property
```

| Token | Description |
|-------|-------------|
| list_name | The name of the list to get value(s) from |
| match_property | The property used to look up an item |
| ${q1} | A value used to look up the item (e.g., a unique id) |
| desired_property | The property whose value is returned |

### Example queries

| Example query | Description |
|---------------|-------------|
| `instance('crops')/root/item[name = ${crop}]/average_yield` | In a list of crops, use a previously-entered crop name to look up that crop's average yield. |
| `instance('days')/root/item[number = ${day_number}]/blood_pressure_needed` | In a list of days, use a previously-entered day number to look up whether or not blood pressure needs to be entered that day. |
| `instance('data_collectors')/root/item[name = ${data_collector_id}]/visits_complete` | In a list of data collectors, use a previously-entered data collector id to look up how many visits the data collector completed. |
| `instance('houses')/root/item[name = ${hhid}]/geometry` | In a list of houses, use a previously-entered household id to look up the geometry (location) of that house. |
| `count(instance('participants')/root/item[answered = 'no']/label)` | Filter a list of participants to only those who have not answered yet. Count the number of matches. |
| `sum(instance('patients')/root/item[birth_year > 1978][gender = 'female']/child_count)` | Filter a list of patients to only those with birth year greater than 1978, then filter that list to females only, then get the child count for each of them. The full expression computes the sum of all children of females born after 1978. |
| `instance('children')/root/item[household = ${hhid}][number = position(..)]/first_name` | Get the first name of the Nth child in a household with id ${hhid} where N is the position of the current repeat in the list of all repeat instances. |

- You can also use these expressions with repeats. For example, if `${repeat}` is a repeat in your form, you can write `${repeat}[filter expression]/desired_field` to access a field in a repeat instance or `sum(${repeat}[filter expression]/desired_field)` to get the sum of all repeat instances that match the filter.
- If you are looking values up in an attached CSV, you can also use the `pulldata` function. In Collect, this may have better performance for lists with many tens of thousands of elements.

```
pulldata(list_name, desired_property, match_property, ${q1})
```

This is equivalent to:

```
instance('list_name')/root/item[match_property = ${q1}]/desired_property
```

## Additional columns

The default survey, choices, settings and entities sheets include the most common columns. You may find it useful to add some of the columns described below.

### Survey sheet

| Column | Description | Collect mobile | Enketo web | Translatable |
|--------|-------------|:--------------:|:----------:|:------------:|
| save_to | When using Entities, used to specify the Entity property to save the form field's value to | Yes | Yes | N/A |
| guidance_hint | Used to display additional information to data collectors in a way that is less visible than hints | Yes | Yes | Yes |
| required_message | A custom message to replace the generic message when a required value is not filled in | Yes | Yes | Yes |
| read_only | An expression used to determine whether the question's value can be edited or not | Yes | Static values only (yes/no) | N/A |
| big-image | The filename of an image to display when the label image is tapped. Can be translated. | Yes | Yes | Yes |
| instance::my_attribute | A custom instance attribute which will be included along with its value in submitted data | Yes | Yes | N/A |
| bind::my_attribute | A custom bind attribute. Generally used when an application has a special custom feature | Yes | Yes | N/A |
| body::my_attribute | A custom body attribute. Generally used when an application has a special custom feature | Yes | Yes | N/A |

### Choices sheet

| Column | Description | Collect mobile | Enketo web | Translatable |
|--------|-------------|:--------------:|:----------:|:------------:|
| image | An image to show with this choice | Yes | Yes | Yes |
| audio | An audio file to show with this choice | Yes | Yes | Yes |
| video | A video file to show with this choice | Yes | Yes | Yes |
| geometry | A special column that represents a choice's point, trace or shape to be displayed on a map with the map appearance | Yes | No | N/A |

### Settings sheet

| Column | Description | Collect mobile | Enketo web | Translatable |
|--------|-------------|:--------------:|:----------:|:------------:|
| public_key | Public key for encrypting form instances when your server is not trusted | Yes | Yes | N/A |
| submission_url | Specify a URL to make submissions to regardless of how the app is configured | Yes | No | N/A |
| allow_choice_duplicates | Add with 'yes' value if you want a single list on the choices sheet to have multiple choices with the same name | Yes | Yes | N/A |

### Entities sheet

| Column | Description | Collect mobile | Enketo web | Translatable |
|--------|-------------|:--------------:|:----------:|:------------:|
| create_if | A condition that a filled form must meet for an entity to be created from it | Yes | Yes | N/A |
| entity_id | The id of the entity that should be updated by filling this form | Yes | Yes | N/A |
| update_if | A condition that a filled form must meet for an entity to be updated from it (with entity_id) | Yes | Yes | N/A |

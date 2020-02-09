# YAMD

> It reads like Markdown, writes like Markdown, converts like Markdown. Is it Markdown?

> **Y**AMD **a**in't **M**ark**d**own

### Introduction

YAMD is a data serialization standard with Markdown-*like* syntax.

Proper documentation for primary data collection can be hard, especially if it
involves qualatative sources.
Unless working on a project with specialized tools available, researchers
may start with two programs: a spreadsheet application (like Excel) 
and a word processor (like Word).
They consult some sources, decide to add new things to the dataset,
made changes to the spreadsheet, document said changes in the word file, and so on.

With YAMD, researchers can focus on the documentation exculsively during the data collection
stage, and let [available tools](#tool) generates the dataset afterwards.
This also make checking and updating dataset a lot easier.
Instead of (1) checking the documentation, and then (2) making sure the spreadsheet correctly
reflects the decisions of the documentation, researchers now only have to do the former.
Once the YAMD file is updated, the dataset generated from it would faithfully reflect the updates.

### Syntax

YAMD syntax is designed to strike a balance between readability and functionality, and
is modeled after [CommonMark](https://commonmark.org/help/).
YAMD files, if written in accordance to Markdown conventions (which in not necessarily),
can be processed as a Markdown file by any file converter.

#### assigning variables

Example:
```markdown
- variable 1: value 1
* variable 2: 2
- variable 3: 3.14
```
produces:
```json
{
  "variable 1": "value 1", 
  "variable 2": 2, 
  "variable 3": "3.14"
}
```
There is no need to quote strings.
Note that floats would be stored as strings.

YAMD is forgiving about spaces and line breaks, but you probably want to 
stick to Markdown conventions regarding indentation.

Example:
```markdown
- variable 1:value 1
 * variable 2: 2
     -       variable   3   :   3.14
```
produces:
```json
{
  "variable 1": "value 1", 
  "variable 2": "value 2",
  "variable 3": "value 3"
}
```

If no value is assigned to a variable, a default string "NA" would be assinged.

Example:
```markdown
- variable 1: value 1
- variable 2: 
- variable 3: value 3
```
generates

```json
{
    "variable 1": "value 1", 
    "variable 2": "NA", 
    "variable 3": "value 3"
}
```
### Tool

- [pyamd](https://github.com/chmlee/pyamd): YAMD parser and dumper for Python (under active production)

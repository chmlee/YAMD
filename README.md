# YAMD

> It reads like Markdown, writes like Markdown, converts like Markdown. Is it Markdown?

> **Y**AMD **a**in't **M**ark**d**own

## Introduction

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

## Feature
- Syntax compatible with Markdown, and can be processed as such
- Support nested data structure
- List of Entries

## Syntax

YAMD syntax is designed to strike a balance between readability and functionality, and
is modeled after Markdown.
YAMD files, if written in accordance to Markdown conventions (not necessarily but highly recommended),
can be processed as a Markdown file by any file converter.

To illustrate the syntax, I'll collect information of the the three ethnic groups in Belgium as an exmaple.

### assigning strings

Variables are assigned in the form of `- <key>: <value>`

Say I'm collecting information of the group
Example:
```markdown
- variable 1: value 1
- variable 2: value 2
- variable 3: value 3
```
produces:
```json
{
  "variable 1": "value 1", 
  "variable 2": "value 2", 
  "variable 3": "value 3"
}
```
There is no need to quote strings.

YAMD is forgiving about leading and trailing spaces, but you probably want to stick to Markdown conventions regarding indentation.

Example:
```markdown
- variable 1:value 1
 - variable 2: value 2
     -       variable   3   :   value  3
```
also produces:
```json
{
  "variable 1": "value 1", 
  "variable 2": "value 2",
  "variable   3": "value  3"
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
### assigning numbers

Example:
```markdown
- variable 1: 1
- variable 2: -2
- variable 3: 3.14159
- variable 4: 4-2i
```
produces:
```json
{
  "variable 1": 1, 
  "variable 2": -2, 
  "variable 3": 3.14159,
  "variable 4": "4-2i"
}
```
Complex numbers are stored as strings

### assigning lists

There are three methods to store lists: horizontal list, vertical list and List of Entries (LOE).
They all achieve the same goal, but based on the type of element your list contain, one might be more 
visually appealing then others.

#### horizontal list

Items of a horizontal list is assigned in the form of
`- <key>: [ <item>, <item>, ... , <item> ]`

Example:
```markdown
- list 1: [1, 2, 3.14159]
- list 2: ["text 1", 'text 2', "text 3"]
- not a list: [text a, text b, text c]

```
generates:
```
{
  "list 1": [1, 2, 3.14159], 
  "list 2": ["text 1", "text 2", "text 3"]
  "not a list": "[text a, text b, text c]"  
}
```
Note that strings requires either single or double quotes, or else the entire list would be stored
as one string.

#### vertical list

Items of a vertical list is assigned in the form of:
```
- <key>:
  * <item>
  * <item>
  ...
  * <item>
```

Example
```markdown
- list 1:
  * 1
  * 2
  * 3.14159
- list 2:
  * text 1
  * text 2
  * text 3
- still a list:
* text a
* text b
* text c
```
generates
```json
{
  "list 1": [1, 2, 3.14159], 
  "list 2": ["text 1", "text 2", "text 3"], 
  "still a list": ["text a", "text b", "text c"]
}
```
There is no need to quote strings in vertical lists.
Also, although YAMD ignored leading spaces, it is recommended to stick to the Markdown conventions and indent each item.

#### List of Entries

Example:
```markdown
# entry
- variable 1: a
- variable 2: A

# entry
- variable 1: b
- variable 2: B

# entry
- variable 1: c
- variable 2: C
```
generates
```json
{
  "entry": [
    {
      "variable 1": "a", 
      "variable 2": "A"
    }, 
    {
      "variable 1": "b", 
      "variable 2": "B"
    }, 
    {
      "variable 1": "c", 
      "variable 2": "C"
    }
  ]
}
```


### Tool

- [pyamd](https://github.com/chmlee/pyamd): YAMD parser and dumper for Python (under active production)

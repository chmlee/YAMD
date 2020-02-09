
# YAMD

> It reads like Markdown, writes like Markdown, converts like Markdown. Is it Markdown?

> **Y**AMD **a**in't **M**ark**d**own

## Introduction

YAMD is a data serialization standard with Markdown-*like* syntax.

Accurate documentation for primary data collection can be hard, especially if it
involves qualitative sources.
Unless working on a project with specialized programs available, researchers
usually start with two basic programs: a spreadsheet application (like Excel) 
and a word processor (like Word).
They consult some sources, decide to add new things to the dataset,
made changes to the spreadsheet, document said changes in the word file, and so on.

With YAMD, researchers can focus on the documentation exclusively during the data collection
stage, and let [available tools](#tool) generates the dataset afterwards.
This also make checking and updating dataset a lot easier.
Instead of (1) checking/updating the documentation, and then (2) making sure the spreadsheet correctly
reflects the decisions of the documentation, researchers now only have to do the former,
Once the YAMD file is updated, the dataset generated from it would faithfully reflect the changes.

## Feature
- Syntax compatible with Markdown, and can be processed as such
- Support nested data structure
- [List of Entries (LOE)](#list-of-entries-loe)

## Syntax

YAMD syntax is designed to strike a balance between readability and functionality, and is
inspired by Markdown.  YAMD files, if written in accordance to Markdown conventions (not necessarily but highly recommended),
can be processed as a Markdown file by any file converter.

YAMD has two basic structures: `<key-value pair>` and `<dictionary>`. 
`<key-value pair>` assigns a value to a key in the form of `- <key>: <value>`. 
`<dictionary>` is a collection of `<key-value pair>`

`<key>` is case sensitive, and is always stored as a string. 
It can contain character, numbers, spaces and symbols except backslash `\`.
Some examples are:

`key`,

`key_one`,

`keyTwo`,

`a long key name with spaces`,

`1`,

`-2`,

`3.14`,

`^%&*`

### strings

Example:
```markdown
- key 1: value 1
- key 2: value 2
- key 3: value 3
```
generate
```json
{
  "key 1": "value 1", 
  "key 2": "value 2", 
  "key 3": "value 3"
}
```
There is no need quote strings; quotation marks will be stored as it is:
```markdown
- key 1: value 1
- key 2: 'value 2'
- key 3: "value 3"
```
generates
```json
{
  "key 1": "value 1", 
  "key 2": "'value 2'", 
  "key 3": "\"value 3\""
}
```

YAMD is forgiving about leading and trailing spaces, but you probably want to stick to Markdown conventions regarding indentation.

Example:
```markdown
- variable 1:value 1
 - variable 2: value 2
     -       variable   3   :   value  3
```
produces:
```json
{
  "variable 1": "value 1", 
  "variable 2": "value 2",
  "variable   3": "value   3"
}
```

### numbers

Example:
```markdown
- key 1: 1
- key 2: -2
- key 3: 3.14159
- key 4: 1.
- key 5: 1.0
- key 6: .1
```
generates
```json
{
  "key 1": 1, 
  "key 2": -2, 
  "key 3": 3.14159, 
  "key 4": 1.0, 
  "key 5": 1.0, 
  "key 6": 0.1
}
```

Both integers and floats are accepted. 

To force a number to be stored as a string, wrap it with either single or double quotation marks:

Example:
```makrdown
- key 1: 1
- key 2: '1'
- key 3: "1"
- key 4: '1.0'
- key 5: '1.0 '
```
generates
```json
{
  "key 1": 1, 
  "key 2": "1", 
  "key 3": "1", 
  "key 4": "1.0", 
  "key 5": "'1.0 '"
}
```

Compare the results for `key 4` and `key 5`. Note that the string wrapped by quotation marks must be a valid number, 
or else the entire value would be interpreted as a string and include extra quotation marks. 

Storing numbers as strings may sometimes be a must:
```markdown
- key 1: 0012
- key 2: '0012'
```
generates:
```json
{
  "key 1": 12,
  "key 2": "0012"
}
```

### Boolean

Boolean values are `TRUE` and `FALSE`, both capitalized.

```markdown
- key 1: TRUE
- key 2: FALSE
- key 3: true
- key 4: false
```
generates
```json
{
  "key 1": true, 
  "key 2": false, 
  "key 3": "true", 
  "key 4": "false"
}
```
Note that the values must be exact matches, or else they are stored as strings.

### "no response"

To store "no response", either assign `NA` or leave the value blank.

Example:
```markdown
- key 1: value 1
- key 2: 
- key 3: NA
- key 4: null
```
generates

```json
{
  "key 1": "value 1", 
  "key 2": null, 
  "key 3": null
  "key 4": "null"
}
```

### lists

There are three types of lists: horizontal list, vertical list and List of Entries (LOE).
The first two types store list of strings and/or numbers, and the thrid stores list of dictionaries.

#### horizontal list

Items of a horizontal list is assigned in the form of
`- <key>: [ <item>, <item>, ... , <item> ]`

Example:
```markdown
- list 1: [1, 2, 3.14159]
- list 2: ["text 1", 'text 2', "text 3"]
- not a list: [text a, text b, text c]

```
generates
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

Items of a vertical list are assigned in the form of:
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

#### List of Entries (LOE)

Example:
```markdown
# entry
- variable 1: a
- variable 2: A

# entry
- variable 3: b
- variable 4: B
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
      "variable 3": "b", 
      "variable 4": "B"
    }
  ]
}
```

For dictionaries to be stored in the same list, they need to have the same LOE names.
However, the keys of the dictionaries need not be identical.

LOE can also be nested:

```markdown
# entry
  - variable 1: value 1
  ## subentry
    - variable 2: value a
    - variable 3: value b
  ## subentry
    - variable 4: value c
    - variable 5: value d
# entry
  - variable 1: value 2
  ## subentry
    - variable 2: value e
    - variable 3: value f
  ## subentry
    - variable 4: value g
    - variable 5: value h
```
generates
```json
{
  "entry": [
    {
      "variable 1": "value 1", 
      "subentry": [
        {
          "variable 2": "value a", 
          "variable 3": "value b"
        }, 
        {
          "variable 4": "value c", 
          "variable 5": "value d"
        }
      ]
    }, 
    {
      "variable 1": "value 2", 
      "subentry": [
        {
          "variable 2": "value e", 
          "variable 3": "value f"
        }, 
        {
          "variable 4": "value g", 
          "variable 5": "value h"
        }
      ]
    }
  ]
}
```
The number of leading `#` of the list name determine how it is nested.
The nesting rule is simple: the lines following the name are together *one* element of the list,
until the line with LOW with the same or more numbers of `#`
YAMD supports up to 7 level of LOE.

```markdown
# level 1
  - variable 1: value 1
  ## level 2
    - variable 2: value 2
    ### level 3
      - variable 3: value 3
      #### level 4
        - variable 4: value 4
        ##### level 5
          - variable 5: value 5
          ###### level 6
            - variable 6: value 6
  ## level 2
    - variable 7: value 7
    ## level 3
      - variable 8: value 8
      ### level 4
        - variable 9: value 9
  ## LEVEL 2
    - variable 10: value 10
```
generates
```jsos
{
  "level 1": 
  {
    "variable 1": "value 1", 
    "level 2": [
      {
        "variable 2": "value 2", 
        "level 3": 
        {
          "variable 3": "value 3", 
          "level 4": 
          {
            "variable 4": "value 4", 
            "level 5": 
            {
              "variable 5": "value 5", 
              "level 6": 
              {
                "variable 6": "value 6"
              }
            }
          }
        }
      }, 
      {
        "variable 7": "value 7"
      }
    ], 
    "level 3": 
    {
      "variable 8": "value 8", 
      "level 4": 
      {
        "variable 9": "value 9"
      }
    }, 
    "LEVEL 2": 
    {
      "variable 10": "value 10"
    }
  }
}
```

## Tool

- [pyamd](https://github.com/chmlee/pyamd): YAMD parser and dumper for Python (under active production)

Future plans (no guarantee):
- yamdit: a fork of [StackEdit](https://stackedit.io/) modified for editing YAMD files.
- pymad port to R

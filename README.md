# REAM

> It reads like Markdown, writes like Markdown, converts like Markdown. Is it Markdown?

> **RE**AM **a**in't **M**arkdown

## Introduction

REAM is a data collection tool that encourages documentating data generating process.
It includes three components:

1. [ream-lang](https://www.github.com/chmlee/ream-lang):
a data serialization standard with Markdown-like syntax

2. [ream-python](https://www.github.com/chmlee/ream-python):
a parser and emitter for REAM files written in Python

3. ream-editor:
a GUI application to edit REAM file (development in process)


The remaining of this article introduces the serialization standard.

## Design Principles

- Readability matters
- Markdown compatibility, and can be processed as such
- serves as the dataset and the documentation

![ream diagram](./diagram.png)

## Specifications
- Case sensitive
- Indentation insensitive: leading and trailing whitespaces, that is, tabs (`0x09`) and spaces (`0x20`), are ignored

## Basic Structures

REAM has two basic structures:

- `<variable>`: `<key>` - `<value>` pair
- `<entry>`: a collection of `<variable>`

`<value>` can be any of the following types:

- [String](#string)
- [Number](#number)
- [Boolean](#boolean)
- [NA](#na)
- [Star List](#star-list)

## Variable

`<variable>` assigns a `<value>` to a `<key>`, in the form of

```markdown
- <key>: <value>
```

`<variable>` starts with a dash `-`.
`<value>` and `<key>` are separated by `:`.

`<key>` can't be empty.
It can consists of upper and lowercase letters (A-Z, a-z), digits (0-9) and spaces (`0x20`).

### String

Example:
```markdown
- key 1: value 1
- key 2: value 2
- key 3: value 3
```

There is no need quote strings; quotation marks will be stored as it is.

```markdown
- key 1: value 1
- key 2: 'value 2'
- key 3: "value 3"
```
translates to the following JSON expression:
```json
{
  "key 1": "value 1",
  "key 2": "'value 2'",
  "key 3": "\"value 3\""
}
```

Recall that REAM ignores all leading and trailing spaces.
So

```markdown
- key 1:value 1
 - key 2: value 2
     -       key 3   :   value 3
-key 4:value 4
```
is equivalent to
```markdown
- key 1: value 1
- key 2: value 2
- key 3: value 3
- key 4: value 4
```

Nevertheless, it is recommended to stick to one Markdown flavor regarding indentation and be consistent throughout the file so as to be markdown-compatible.

### Number

Numbers are surrounded by question marks (`$`)

#### Number Literals

```markdown
- number 1: $1$
- number 2: $-2$
- number 3: $3.1415926$
```

Underscores can be used as separators to improve readability:

```markdown
- number 4: $123_456_789$
- number 5: $1_2345_6789$
```

#### Scientific Notation

```markdown
- number 6: $3e8$
- number 7: $6.02E23$
```

#### Latex Number

(experimental)

Latex commands can be used to represent numbers that can't be represent by finite number literals.

```markdown
- number 6: $\frac{1}{3}$
- number 6: $1/3$
- number 7: $\pi$
```

### Boolean

Boolean values are \`TRUE\` and \`FALSE\`, both uppercase and surrounded by backquotes.

```markdown
- bool 1: `TRUE`
- bool 2: `FALSE`
- not bool 1: TRUE
- not bool 2: `True`
```

Note that the boolean values must be exact matches, or else they are stored as strings.

### NA

Assign \`NA\` to store "no response"

```markdown
- valid na: `NA`
- invalid na 1: NA
- invalid na 2: `Na`
```

Again, if the value is not an exact match to \`NA\`, it is stored as a string.

### Star List

Star lists are a sequence of strings, numbers, boolean and/or NA, in the form of:

```
- <key>:
  * <item>
  * <item>
  ...
  * <item>
```

Example:
```markdown
- list of string:
  * text 1
  * text 2
  * text 3
- list of numbers:
  * $1$
  * $2$
  * $3.14159$
- still a list:
* text a
* text b
* text c
```

## Comment

Comments follow strings, numbers, booleans and NA, in the form of

```markdown
- <key>: <value>
  > <comment>
```
for variables, and

```markdown
- <key>: <value>
  * <item>
    > <comment>
```
for star list.

Again, all white spaces proceeding the greater-than sign `>` will be ignored, so choose the indentation scheme that suits you the most.

## Entry

Entry is a collection of variables, in the form of

```markdown
# <entry name>
- <key 1>: <value 1>
- <key 2>: <value 2>
...
- <key n>: <value n>
```

Entries must have unique variables.

For example:
```markdown
# Country
- name: The Kingdom of Belgium
- capital: Brussels
- population: $11_433_256$
- permanent member of UNSC: `FALSE`
- languages:
  * Dutch
    > Official language
  * French
    > Official language
  * German
    > Official language
```

This corresponds to the following JSON:

```json
{
    "Country": [
        {
            "name": "The Kingdom of Belgium",
            "capital": "Brussels",
            "population": "1146000",
            "permanent member of UNSC": "FALSE",
            "languages": [
                "Dutch__COM__Official language",
                "French__COM__Official language",
                "German__COM__Official language"
            ]
        }
    ]
}
```

Entries can be nested, and the nest level is indicated by the number of pound sign `#` proceeding the entry name.

For example:

```markdown
# Country
- name: The Kingdom of Belgium
- capital: Brussels
- population: $11_433_256$
- permanent member of UNSC: `FALSE`

## language
- name: Dutch
- official language: `TRUE`
- size: $0.6$
  > source: CIA World Factbook

## language
- name: French
- official language: `TRUE`
- size: $0.4$
  > source: CIA World Factbook

## language
- name: German
- official language: `TRUE`
- size: less than 0.01
  > source: CIA World Factbook
```

This is equivalent to the following JSON:

```json
{
    "Country": [
        {
            "name": "The Kingdom of Belgium",
            "capital": "Brussels",
            "population": "1146000",
            "permanent member of UNSC": "FALSE",
            "language": [
                {
                    "name": "Dutch",
                    "official language": "TRUE",
                    "size": "0.6__COM__source: CIA World Factbook"
                },
                {
                    "name": "French",
                    "official language": "TRUE",
                    "size": "0.4__COM__source: CIA World Factbook"
                },
                {
                    "name": "German",
                    "official language": "TRUE",
                    "size": "less than 0.01__COM__source: CIA World Factbook"
                }
            ]
        }
    ]
}
```

REAM supports up to 6 nest levels.

```markdown
# Country
- name: The Kingdom of Belgium
- code: BE

## Region
- name: Brussels-Capital Region
- code: BRU

## Region
- name: Flemish Region
- code: VLG

### Province
- name: Antwerpen
- code: VAN

### Province
- name: Limburg
- code: VLI

### Province
- name: Oost-Vlaanderen
- code: VOV

### Province
- name: Vlaams-Brabant
- code: VBR

### Province
- name: West-Vlaanderen
- code: VWV

## Region
- name: Walloon Region
- code: WAL

### Province
- name: Brabant wallon
- code: WBR

### Province
- name: Hainaut
- code: WHT

### Province
- name: Liège
- code: WLG

### Province
- name: Luxembourg
- code: WLX

### Province
- name: Namur
- code: WNA
```
maps to the following JSON:

```json
{
    "Country": [
        {
            "name": "The Kingdom of Belgium",
            "code": "BE",
            "Region": [
                {
                    "name": "Brussels-Capital Region",
                    "code": "BRU"
                },
                {
                    "name": "Flemish Region",
                    "code": "VLG",
                    "Province": [
                        {
                            "name": "Antwerpen",
                            "code": "VAN"
                        },
                        {
                            "name": "Limburg",
                            "code": "VLI"
                        },
                        {
                            "name": "Oost-Vlaanderen",
                            "code": "VOV"
                        },
                        {
                            "name": "Vlaams-Brabant",
                            "code": "VBR"
                        },
                        {
                            "name": "West-Vlaanderen",
                            "code": "VWV"
                        }
                    ]
                },
                {
                    "name": "Walloon Region",
                    "code": "WAL",
                    "Province": [
                        {
                            "name": "Brabant wallon ",
                            "code": "WBR"
                        },
                        {
                            "name": "Hainaut ",
                            "code": "WHT"
                        },
                        {
                            "name": "Li\u00e8ge",
                            "code": "WLG"
                        },
                        {
                            "name": "Luxembourg",
                            "code": "WLX"
                        },
                        {
                            "name": "Namur ",
                            "code": "WNA"
                        }
                    ]
                }
            ]
        }
    ]
}
```

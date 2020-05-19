# REAM

> It reads like Markdown, writes like Markdown, converts like Markdown. Is it Markdown?

> **RE**AM **a**in't **M**arkdown

## Introduction

REAM is a data collection tool that encourages documentation for data generating process.
It includes three components:

1. a data serialization standard with Markdown-like syntax
2. a parser/dumper for REAM files written in Python
3. a CRUD application to create, read, update and delete REAM file content

The remaining of this article introduces the serialization standard.
For further information on the parser/dumper, see [ream-python](#).
For further information on the CRUD application, see [ream-edit](#).

## Design Principles

- Readability matters
- Markdown compatibility, and can be processed as such
- REAM file is the dataset *and* the documentation
- One-to-one with JSON

## Specifications
- Case sensitive
- Indentation insensitive: leading whitespaces, that is, tabs (`0x09`) and/or spaces (`0x20`), are ignored

## Basic Structures

REAM has three basic structures:

- `<variable>`: `<key>` - `<value>` pair
- `<entry>`: a collection of `<variable>`
- `<comment>`

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
`<value>` and `<key>` is separated by `:`.

`<key>` can't be empty.
It can consists of upper and lowercase letters (A-Z, a-z), digits (0-9) and spaces (`0x20`), but must not starts or ends with spaces.

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
is equivalent to the following JSON expression:
```json
{
  "key 1": "value 1",
  "key 2": "'value 2'",
  "key 3": "\"value 3\""
}
```

REAM is ignores all leading and trailing spaces.

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

Nevertheless, you probably want to stick to one Markdown convention regarding indentation and be consistent throughout the file if you wish the file to be markdown-compatible.

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
- number 6: 3e8
- number 7: 6.02E23
```

#### Latex Number

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

Assign `NA` to store "no response"

```markdown
- valid na: `NA`
- invalid na 1: NA
- invalid na 2: `Na`
```

Again, if the value is not an exact matches to \`NA\`, it is stored as a string.

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

For example:
```markdown
# Country
- name: The Kingdom of Belgium
- capital: Brussels
- population: $1146000$ 
- permanent member of UNSC: `false`
- languages:
  * Dutch
    > Official language
  * French
    > Official language
  * German
    > Official language
```

Entries can be nested, and the nest level is indicated by the number of pound sign `#` proceeding the entry name.

For example:

```markdown
# Country
- name: The Kingdom of Belgium
- capital: Brussels
- population: $1146000$ 
- permanent member of UNSC: `false`

## language
- name: Dutch
- official language: `ture`
- size: $0.6$
  > source: CIA World Factbook

## language
- name: French
- official language: `ture`
- size: $0.4$
  > source: CIA World Factbook

## language
- name: German
- official language: `ture`
- size: less than 0.01
  > source: CIA World Factbook
```

This is equivalent to the following JSON:

```json
{
  "Country": 
    [
      {
        "name": "The Kingdom of Belgium", 
        "capital": "Brussels", 
        "population": "$1146000$", 
        "permanent member of UNSC": "`false`", 
        "language": 
          [
            {
              "name": "Dutch", 
              "official language": "`ture`", 
              "size": "$0.6$__COM__source: CIA World Factbook"
            }, 
            {
              "name": "French", 
              "official language": "`ture`", 
              "size": "$0.4$__COM__source: CIA World Factbook"
            }, 
            {
              "name": "German", 
              "official language": "`ture`", 
              "size": "less than 0.01__COM__source: CIA World Factbook"
            }
          ]
      }
    ]
}
```

REAM is **not** a Markdown flavor.
By design, REAM file should be able to be processed as a Markdown file by most
mainstream file converter, but only a subset of Markdown syntax is supported by
REAM.
REAM is, after all, a data serialization standard, and should be only used for
such purpose.

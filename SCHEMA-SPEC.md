# JSL Specification

This is a specification of JSL, including data model and grammar.

JSL is intended to be fully compatible with original [SDLang](https://sdlang.org) specification, while adding new features. This means that any JSL parser implementations will be able to parse any SDLang file, but SDLang parser implementations will not be able to parse some or most JSL files. 

This document is a draft.

<!-- TODO: add links and clean up examples -->

<!-- TODO: what about $ ! @ at start of node names? -->
<!-- TODO: Add prefix property for them!!! -->

## Overview

**Example 1: Creating a List**

```c
shopping_list 1 "mayo" 2 "tomatoes" 12 "strawberries"
```

**Example 2: Creating a Map**

```c
alias_dir main="/tree/index/main/" home="/home/user/" projects="/mnt/projects" 
```

**Example 3: Creating a Tree**

```c
index {
    books {
        fantasy {}
        detective {}
        romance {}
    }
    movies {
        thriller {}
        action {}
        horror {}
    }
}
```

**Example 4: Creating a Matrix**

```c
transform {
    // Each line is a list
    // of anonymous nodes
    1 0 0 0
    0 1 0 0
    0 0 1 0
    0 0 0 1
}
```

**Example 5: A Tree of Nodes with Values and Attributes**

```d
doctype "html"
html {
    head {
        meta httv-equiv="Content-Type" content="text/html; charset=UTF-8"
        meta name="viewport" content"width=device-width, initial-scape=1"
        
        title "The Website"

        link rel="icon" href="/favicon.ico"

        script defer src="/js/index.js"

        link ref="stylesheet" type="text/css" href="/css/index.css"

        script `
            if (/Mobi|Android/i.test(navigator.userAgent)) {
                window.open('/mobile.html', '_self');
            }
        `
    }
    body {
        div id="title" {
            h1 "This is my pretty website"
        }
        noscript {
            h1 "Your browser does not support JavaScript!"
            h3 "Please enable it as this website cannot run without it."
        }
    }
}
```

## Filetype

JSL files are files with extension `.jsl` and should be encoded using UTF-8.

---

JSL files (any file ending with the extension .jsl) should always be encoded using UTF-8. The use of unicode escaping (such as the \uxxxx format used by Java and C#) is not supported or required. Non ASCII characters should be entered directly using a UTF-8 capable editor.

**Note**: ASCII is transparently encoded in UTF8, so ASCII files can be used if only ASCII characters are required.

## Components

### Document

Contents of JSL file is considered a document. Document is composed of zero or more [nodes](#Node), separated by whitespace characters and terminated by EOF.

**Example:**
```c
node {
    child
}
list 0 1 2
```

### Node

Node is a core component of JSL. Node is comprised by a name, [arguments](#arguments), [properties](#properties) and [children](#children) separated by either space or [slash-escaped line continuation](#line-continuation).

[Arguments](#arguments) and [properties](#properties) must follow node's [name](#name) and can be placed in any order. Node's [children](#children) must be placed after node's [arguments](#arguments) and [properties](#properties).  

Node is terminated by either a newline, a semicolon (`;`), an ending bracket of a [child list](#children) (`}`) or the end of file/stream (an `EOF`). However node is not terminated if last character in line is a [backslash](#line-continuation) (`\`)

**Example:**
```c
namespace:name property=value argument {
    child
}

namespace:name arg1 arg2 \
    arg3 arg4; new_node arg { child1; child2 }
```

#### Name

[Node](#node) name starts with a unicode letter or underscore (\_) followed by zero or more unicode letters, numbers, underscores (\_), dashes (-), periods (.) and dollar signs ($). Examples of valid [node](#node) names:

* myName
* myName123
* my-name
* \_my\_name$\_
* com.domain.foo

[Node](#node) name can also contain an optional namespace which is prefixing the name, separated by `:` and follows the same naming rules:

* nameSpace:myName
* n4m35p4c3:myN4m3
* name-space:my-name
* \_name\_space$\_:\_my\_name$\_
* name.space:com.ikayazo.foo

If namespace is not specified it will default to an empty string (`""`) as their name.

Both name and namespace are [node's](#node) identifiers.

#### Arguments

[Node's](#node) arguments are an ordered list of [literals](#literals) which can contain duplicates.

**Example:**
```c
node 1 2 "string" true false null 12:05:41 on off 1 32.5
```

#### Properties

[Node's](#node) properties are a map of key-value pairs which cannot contain duplicates and can be unordered.

Properties contain a [name](#name) and a [literal](#literals) separated by an equal sign (`=`) without any whitespace characters surrounding it.

Properties that do not contain a value are considered to be [boolean type](#boolean) and contain `true` value. If, however, valueless properties are prefixed with an exclamation mark (`!`) they are considered to contain [boolean](#boolean) `false`.

Property names follow the same rules as [node names](#name).

Properties can contain special type of string value called [literal string](#literal-strings) which follows [node name](#name) rules, see `inherit` property in example.

If properties contain duplicate names then rightmost properties will override earlier ones.

**Example:**
```c
node type="item" inherit=abstract:pickup_item stack:count=6 enabled !empty

node prop="not this one" prop="not this one either" prop="now prop is this current string"
```

#### Children

[Node's](#node) children is an ordered list of [nodes](#node), similar to [document](#document). They are indicated surrounded by curly brackets (`{}`) and must be placed last in the [node](#node).

[Node](#node) can contain only one children list.

Opening child brace (`{`) must be placed on a same line as a [node](#node) and there cannot be an [anonymous node](#anonymous-nodes) containing only child list.

**Example:**
```c
node {
    child_node1
    child_node2
    1 2 3 // anonymous node list
}
```

#### Anonymous Nodes

Anonymous nodes contain no name and must start from an [argument](#arguments) to not be confused with [normal nodes](#node). Anonymous node name will default to an empty string (`""`).

**Example:**
```c
1 2 3 type="list"

3 matrix {
    // anonymous lists
    0 1 0
    1 0 0
    1 1 1
}

// This is illegal:
// value=4
// Because it can be confused with:
// value 4
```

### Literals

#### Strings

##### Quoted strings

Quoted strings are indicated with double quotes (`""`). Quoted strings can span across multiple lines if new line is escaped with backslash (`\`).

If [new line is escaped](#line-continuation) then any whitespace characters on new line before non-whitespace characters will be ignored. See in example, second [node](#node) will be translated as `this is quoted multiline string`, without preserving spaces on second line before `m`.

**Example:**
```c
node "this is quoted single-line string"

node "this is quoted \ // with comments
      multiline string"
```

##### Multiline strings

Multiline strings are indicated with backtick characters ("\`") and can span across multiple lines.

Multiline strings will preserve all whitespace characters.

New line in multiline string will be interpreted as single newline character (`\n`) regardless of the platform.

**Example:**
```d
node `this is a 
multiline
    string` property
```

##### Literal strings

Literal strings can be only contained in [properties](#properties) and follow [node name](#name) rules. Literal strings will be interpreted as [quoted strings](#quoted-strings) due to their nature. In example [property's](#properties) value will be `"literal_string:with_name"`, which can be easily split to get namespace and name parts.

**Example:**
```c
node property=literal_string:with_namespace
```

##### Escape Sequences

Unicode characters can be directly inserted into both [quoted](#quoted-strings) and [multiline strings](#multiline-strings), however unicode escapes are still supported. Escaped characters are the backslash character (`\`) followed by an escape sequence, and are interpreted as described in the table:
| Escape Sequence | Meaning |
|:--|:--|
| `\'` | Literal single-quote: `'` |
| `\"` | Literal double-quote: `"` |
| `\?` | Literal question mark: `?` |
| `\\` | Literal backslash: `\` |
| `\0` | Binary zero (`NUL`, `U+0000`). |
| `\a` | `BEL` (alarm) character (`U+0007`). |
| `\b` | Backspace (`U+0008`). |
| `\f` | Form feed (FF) (`U+000C`). |
| `\n` | End-of-line (`U+000A`). |
| `\r` | Carriage return (`U+000D`). |
| `\t` | Horizontal tab (`U+0009`). |
| `\v` | Vertical tab (`U+000B`). |
| `\xnn` | Byte value in hexadecimal, where nn is specified as two hexadecimal digits. For example: `\xFF` represents the character with the value 255. |
| `\n` ||
| `\nn` ||
| `\nnn` | Byte value in octal. For example: `\101` represents the character with the value 65 (`A`). Analogous to hexadecimal characters, the largest byte value is `\377` (= `\xFF` in hexadecimal or 255 in decimal) |
| `\unnnn` | Unicode character `U+nnnn`, where nnnn are four hexadecimal digits. For example, `\u03B3` represents the Unicode character `γ` (`U+03B3` - GREEK SMALL LETTER GAMMA). |
| `\Unnnnnnnn` | Unicode character `U+nnnnnnnn`, where nnnnnnnn are 8 hexadecimal digits. For example, `\U0001F603` represents the Unicode character `U+1F603` (SMILING FACE WITH OPEN MOUTH). |
| `\name` | Named character entity from the HTML5 specification. These names begin with `&` and end with `;`, e.g., `&euro;`. |
| `\` | [Newline escape](#line-continuation). If `\` is a last character in line then newline will be ignored. |

#### Numbers

- Numbers must start with digits from `0` to `9`.
- Numbers may start with either `+` or `-` signs.
- Numbers must contain digits between `0` and `9` which can be separated by `_` for visual clarity.
- Numbers may contain a single decimal separator `.`, followed by more digits.
- Numbers may be optionally followed by `E` or `e`, an optional `-` or `+`, and more digits to represent an exponent value.
- Numbers may end with one or more letters to specify type of number.
- Numbers without an integer digits (i.e `.2`) are illegal.

- Integer (signed 32 bits, i32):
    - 123
- Long Integer (signed 64 bits, i64):
    - 123L
    - 123l
- Float (signed 32 bits, f32):
    - 123.45f
    - 123.45F
- Double Float (signed 64 bits, f64):
    - 123.45
    - 123.45d
    - 123.45D
- Decimal (signed 128+ bits, decimal64 or decimal128):
    - 123.45BD
    - 123.45bd

#### Boolean

Boolean value can be represented with `true`, `false` and `on`, `off`.

Values `true` and `on` represent truthful boolean value, which is either `true` or `1`, depending on implementation.

Values `false` and `off` represent false boolean value, which is either `false` or `0`, depending on implementation.

#### Null

Null value is indicated with `null` and depending on implementation can be `null` or `undefined` or other similar concept.

#### Date and Time

Date can be represented in several ways:

- `yyyy/mm/dd`.
    - Example `2005/12/05`.
    - Kept for compatibility with SDLang.
- `yyyy/mm/dd hh:mm(:ss)(.xxx)(-ZONE)`.
    - Example `2005/12/05 05:21:23.532-JST`.
    - Time is represented with 24 hours.
    - Parts in parenthesis are optional.
    - Kept for compatibility with SDLang.
- `dd/mm/yyyy`.
    - Example `05/12/2005`
- `dd.mm.(yy)yy`.
    - Example `01.12.2005` or `01.12.05`.
    - Parts in parenthesis are optional.
- `YYYY-MM-DD(TH:MM:SS)`.
    - ISO 8601 standard.
    - Example `2010-07-04T07:06:12`
    - Example `2014-12-05`
    - Can represent both date and time
    - Time part is optional

Time span is using the format `(-)(d:)hh:mm:ss(.xxx)`. Examples are:

- 12:14:42 (12 hours, 14 minutes, 42 seconds)
- 00:09:12 (9 minutes, 12 seconds)
- 00:00:01.023 (1 second, 23 milliseconds)
- 23d:05:21:23.532 (23 days, 5 hours, 21 minutes, 23 seconds, 532 milliseconds)

Hours, minutes, and seconds are required - days and milliseconds are optional. If the day component is included it must be suffixed with a lower case 'd'.

Time span can be negative, i.e `-00:08:16` will be interpreted as 8 minutes and 16 seconds ago.

#### Binary

Binary is using format of `[Base64]`. Example is - `[sdf789GSfsb2+3324sf2]`.

**Example:**

```c
key [sdf789GSfsb2+3324sf2] name="my key"
image [
    R3df789GSfsb2edfSFSDF
    uikuikk2349GSfsb2edfS
    vFSDFR3df789GSfsb2edf
]
upload from="some.web" data=[
    R3df789GSfsb2edfSFSDF
    uikuikk2349GSfsb2edfS
    vFSDFR3df789GSfsb2edf
]
```

### Comments

#### Single-Line

Any text after `//`, `#` or `--` and until new line is ignored and is considered whitespace.

**Example:**

```d
// Single line comment
```
```py
# Another single line comment
```
```lua
-- And even more line comments
```

#### Multi-Line

Any text between `/*` and `*/` or between `/+` and `+/` is ignored and is considered whitespace.

Comments starting with `/*` end with first occurrence of `*/`.

Comments starting with `/+` are nesting and can contain multiple `/++/` comments and end with matching `+/`.

Component comments start with `/-` and can be used to comment out entire components. They can be used on:

- A [Node](#node) - entire node is ignored.
- A node's [Argument](#arguments) - argument is ignored.
- A [Property](#properties) key - both property key and value are ignored.
- A [Children Block](#children) - entire block, including all children, are ignored.

**Example:**

```d
/* A multi-line comment
Which is spanning
... multiple lines */
/+A nesting /+ multiline +/
comment
+/
```
```c
/-this_node is "commented" out {
    completely
}

only /-"this string" is commented out

only /-this="property" is commented out

only children /-{
    are
    commented out
}
```

### Line Continuation

A line continuation is a `\` character followed by zero or more whitespace items (including [single-line comments](#single-line) after `\` character in [quoted strings](#quoted-strings)) and an optional [single-line comment](#single-line). It must be terminated by a newline (including the newline that is part of [single-line comments](#single-line)).

**Example:**
```c
node 1 2 \ // comments are allowed
     3 4   // after \

node "my broken \ // will become 
      string"     // "my broken string"
```

## Example JSL File

```c
res:type "resource" size=3 uid="uid://8f8ccsa3"

sub:uid_a2f261cf location="res://assets/skybox/sky04"

res:local "resource_id" type="type" {
    color_hex "#f40a1c"
    color_rgb 125 24 255 2
    color_rgb 0.2 0.4 1 0.6
    sky resource=sub:uid_a2f261cf
    curve 0.1245
    transform {
        1 0 0 0
        0 1 0 0
        0 0 1 0
        0 0 0 1
    }
    enabled on
}

res:external "external_res" type="type" \
             path="res://path/to/resource" local

res:node "name" type="type" parent="parent/path" {
    component "component_name" type="className" !enabled {
        speed 0.24
        gravity 9.8
    }
    component "generic_component" type="imGeneric"
}

/-res:node "draft" type="item" parent="res://assets/items/pawn" {}

prop 3
prop_next "super"
```

## References

* JSL references:
  * [SDLang](https://sdlang.org)
  * [KDL](https://kdl.dev)
* Original SDLang references:
    * Declarative Languages and Frameworks
      * [XML](http://www.w3.org/XML/) (and SGML by extension)
      * [YAML](http://www.yaml.org/)
      * [RDF](http://www.w3.org/RDF/)
      * [PYX](http://www.xml.com/pub/a/2000/03/15/feature/index.html) (link from Pat Niemeyer)
      * [JSON](http://www.json.org/)
      * [OGML](http://wwwhome.cs.utwente.nl/~laarman/ogml/)
    * General Purpose (referenced for grammar and selection of literal types)
      * [Java](http://www.javasoft.com/)
      * [C#](http://www.ecma-international.org/publications/standards/Ecma-334.htm) (and other C family languages)
      * [Python](http://www.python.org/)
      * [Ruby](http://www.ruby-lang.org/en/)
      * [ECMAScript](http://www.ecma-international.org/publications/files/ECMA-ST/Ecma-262.pdf) (Javascript)
      * [Groovy](http://www.groovy-lang.org/)


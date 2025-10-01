# Draft: Simple Hierarchical Data (SHD)
A simple hierarchical data format with the human readability of YAML but much simpler semantics.

## Motivation
The data format should have the following properties:
- be able to replace JSON and YAML in various configuration files
- easily readable by humans
- simple parsing rules
- no surprises
- only one way to do one thing

## Document structure
An SHD document consists of nodes, attributes, comments and/or lists followed by a file end marker.
```
top-node:
  some-attribute: value
  child-node:
    another-attribute: next value
  multi-line-attribute:
      here comes multi
      line text
top-attribute: some value
list-node:
  - item1
  - item2
:
```

### end of file marker

To be able to determine whether a document was cut off,
the end of every document is marked by a line-break and colon (`:`).
After the colon, no more characters are allowed.
```
first-node
second-node
:
```

A document can also be empty:
```
:
```

### Nodes
Node names can contain letters, digits, underscores and dashes (`a-z A-Z 0-9 _ -` without the spaces).
Node names can not be empty.  
The only exception to this is the root of the document, which is an invisible nameless pseudo node.
Nodes can contain either
- any number of nodes and attribute/value pairs
- value list items
- node list items

All child names have to be unique - no two children can have the same name.  
All children of a node have to be indented one level higher than their parent node.

### Indentation
Indentation is always done via spaces. 1 level of indentation = 2 spaces.  
No other whitespace is allowed for indentation.

### Attributes and values
Attributes consist of an attribute name and an associated value.
Values can be single or multi line.
In any case, the interpretation of the value is left to the program reading the file.
There are no data types.
All value data is treated the same with respect to parsing.
Different behavior has to be specified by the consuming application.

#### Single line values
An attribute with a single line value is of the form `name: value`.
The space after the colon is not optional.
The `value` can contain any character other than a line break.
All other characters up to the line break are considered part of the value.
```
attribute: value
```

#### Multi line values
Multi line values are indicated by 2 additional levels of indentation after the value name.
The `value` can contain any character data.
The indentation and the last line break are not part of the value.
```
attribute:
    this value has
    multiple lines
```
Here, the value would be "this value has" + line break + "multiple lines". 

### Lists
Instead of nodes and attribute/value pairs, a node's children can be list items.
A list item starts with a hyphen (`-`) and a space.
The space after the hyphen is not optional.
Either all items of the list are just values:
```
list-of-values:
  - item1
  - item2
```
Or all items are nodes, attribute/value pairs or further lists - all of which can be mixed in one list.
```
list-of-things:
  : attribute1: item1
    attribute2: item2
  : node:
      attribute: value
  : - sub1
    - sub2
  : : attribute: value
    : attribute: value
```

### Comments
Lines where the first non-space character is a hash (`#`) followed by a space are comments.
They will be discarded during parsing.
Comments have to be indented similar to normal attributes.
```
# comment
node:
  # comment
  child: value
multi-line-attribute:
  # first comment line
    first line
  # second comment line
    second line
list:
  # comment before first item
  - first item
  # comment before second item
  - second item
list-of-things:
  # comment before first item
  : first item
  # comment before second item
  : : nested list
    # nested comment
```

# Formal grammar
```
name ::= `[a-zA-Z0-9_-]+` (one or more letters, digits, underscores, or dashes)
value ::= `[^\n\r]*` (any characters except newline/carriage return)
⏎ ::= `\n` | `\r\n` | `\r`
end ::= ":" EOF

document ::= element(0) end | value-item(0) end | element-item(0) end | end
element(n) ::= element(n) element(n) | comment(n) | node(n) | attribute(n) | multi-line-attribute(n) | value-list(n) | element-list(n)
indent(n) ::= "  " * n         (2n spaces)
comment(n) ::= indent(n) "#" value
element-name(n) ::= indent(n) name ":⏎"
node(n) ::= element-name(n) element(n+1)
attribute(n) ::= indent(n) name ": " value ":⏎"
multi-line-attribute(n) ::= indent(n) name ":⏎" multi-line-value(n+2)
multi-line-value(n) ::= indent(n) value "⏎" | indent(n) value "⏎" multi-line-value (n)
value-list(n) ::= element-name(n) value-item(n+1)
value-item(n) ::= indent(n) "- " value
element-list(n) ::= element-name(n) element-item(n+1)
element-item(n) ::= indent(n) ": " element(0) TODO: make this work
```

## Features we might want
Features in this section are not part of the specification, but are being considered for addition. 

### Namespaces

### Schemas

### Tables
If you need a table, embed a CSV table in a multi-line-attribute and use `|` as separator:
```
table:
    | header1 | header2 |
    | cell1   | cell2   |
    | cell3   | cell4   |
```

### Optional additional semantics
These are basic predefined optional semantics for reference in documentation and schemas.
So, you can e.g. say a schema is based on "SHD with basic data types"

#### Basic data types
TODO: Is this useful?

boolean: either `true` or `false`  
integer: 64bit integer  
float: 64bit float  
big-integer: arbitrary length integer  
big-float: arbitrary length float  
text: arbitrary character data  

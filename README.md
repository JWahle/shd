# Draft: Simple Hierarchical Data (shd) or alternatively sYAML (simplified YAML)
A simple hierarchical data format with the human readability of YAML but much simpler semantics.

## Motivation
The data format should have the following properties:
- be able to replace JSON and YAML in various configuration files
- easily readable by humans
- simple parsing rules
- no surprises
- only one way to do one thing

## Document structure
An SHD document consists of nodes, attributes and/or lists followed by a file end marker.
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
The only exception to this is the root of the document, which is an invisible nameless pseudo node.
Nodes can contain either
- any number of nodes and attribute/value pairs
- value list items
- node list items

Nodes can not be empty.  
All child names have to be unique - no two children can have the same name.  
All children of a node have to be indented one level higher than their parent node.

### Indentation
Indentation is always done via spaces. 1 level of indentation = 2 spaces.  
No other whitespace is allowed for indentation.

### Attributes and values
Attributes can contain single or multi line values.
In any case, the interpretation of the value is left to the program reading the file.
There are no data types.
All value data is treated the same with respect to parsing.
Different behavior has to be specified by the consuming application.

#### Single line values
An attribute with a single line value is of the form `name: value`,
where the `name` has the exact same constraints as a node name.
The space after the colon is not optional.
The `value` can contain any character other than a line break.
All other characters up to the line break are considered part of the value.
```
attribute: value
```

#### Multi line values
Multi line values are indicated by 2 additional levels of indentation after the value name.
The attribute name has where the `name` has the exact same constraints as a node name.
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
  - attribute1: item1
    attribute2: item2
  - node:
      attribute: value
  - - sub1
    - sub2
```

### Comments
Lines where the first non-tab character is a hash (`#`) followed by a space are comments.
They will be discarded during parsing.
Same indentation as with attributes.
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
```

TODO: should we allow comments in nested lists?

# Formal grammar
TODO: give formal syntax

number ::= integer | float
expression ::= number OP number | expression OP expression | (expression)
OP ::= + | - | * | /

## Features we might want
Features in this section are not part of the specification, but are being considered for addition. 

### Namespaces

### Schemas

### Tables
```
table:
  | header1 | header2 |
  |---------|---------|
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

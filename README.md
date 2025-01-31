# Draft: Simple Hierarchical Data (shd) or alternatively sYAML (simplified YAML)

A simple hierarchical data format with the human readability of YAML but much simpler parsing rules. 

## Motivation

## Features

### No data types

Interpretation is left to the program reading the file.

### ways to define nodes and attributes

Node names can contain `a-z A-Z 0-9 . -`, but no spaces.

```
root:
  attribute1: value
  multi-line:
      here comes multi line text
    next-attribute-here: next value
```

### Lists

Instead of normal nodes, all nodes on a specific level can be list items. Either all value items or all node items.

```
list-of-values:
  - item1
  - item2
```

```
list-of-nodes:
  - attribute1: item1
    attribute2: item2
  - node:
      attribute: item
```

### end of file marker
TODO: maybe use a character that is not allowed for node names? (e.g. ] or })

```
first-node
second-node
end
```

### comments
TODO: Do we need this, or could this be part of the program semantics?

Lines where the first non-tab character is `#`.
Same indentation as with attributes.
TODO: Only allow whole line comments?
Alternatives:
- escape by putting double hash (##)
- quote values with '' or ""
There are only whole line comments because having mid-line comments would only be possible,
if we restrict possible node values or somehow denote value start and end.  

```
# comment
node:
  # comment2
  child: value
  multi-line:
    # first comment line
      first line1
    # second comment
      second line
```

### Namespaces?

### Maybe tables?
```
table:
  | header1 | header2 |
  |---------|---------|
  | cell1   | cell2   |
  | cell3   | cell4   |
```

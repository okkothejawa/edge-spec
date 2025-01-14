## Constants

### Declaration

```ebnf
<constant_declaration> ::= "const" <ident> [<type_signature>] ;
```

Dependencies:

- [`<ident>`](../identifiers.md)
- [`<type_signature>`](../type-system/assignment.md#signature)

The `<constant_declaration>` is a "const" followed by an identifier and optional type signature.

### Assignment

```ebnf
<constant_assignment> ::=
    <constant_declaration> "=" <expr> ;
```

Dependencies:

- [`<expr>`](../expressions.md)

The `<constant_assignment>` is a constant declaration followed by an assignment operator and either
an expression or a comma separated list of identifiers delimited by parentheses followed by a code
block.

> Note: The expression must be a comptime expression, but the grammar should not constrain this.

### Semantics

> todo

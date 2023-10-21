# Type System

The type system builds on core primitive types inherent to the EVM with abstract data types for
parametric polymorphism, nominative subtyping, and compile time monomorphization.

- [Primitive Types](./type-system/primitive-types.md)
- [Type Assignment](./type-system/assignment.md)
- [Product Types](./type-system/product-types.md)
- [Sum Types](./type-system/sum-types.md)
- [Generics](./type-system/generics.md)
- [Trait Constraints](./type-system/traits.md)
- [Implementation](./type-system/implementation.md)
- [Function Types](./type-system/function-types.md)
- [Application Binary Interface](./type-system/abi.md)

## Semantics

All data types are, by default, stack values. If a data type exists in any data location other than
the stack, it must be annotated with a data location. Pairs of data locations and types are distinct
data types. That is to say, for a type `T` in memory (`&m and storage 

## Data Locations

```ebnf
<storage_pointer> ::= "&s" ;
<transient_storage_pointer> ::= "&t" ;
<memory_pointer> ::= "&m" ;
<calldata_pointer> ::= "&cd" ;
<returndata_pointer> ::= "&rd" ;
<internal_code_pointer> ::= "&ic" ;
<external_code_pointer> ::= "&ec" ;

<data_location> ::=
    | <storage_pointer>
    | <transient_storage_pointer>
    | <memory_pointer>
    | <calldata_pointer>
    | <returndata_pointer>
    | <internal_code_pointer>
    | <external_code_pointer> ;
```

The `<location>` is a data location annotation indicating to which data location a pointer's data
exists. We define seven distinct annotations for data location pointers. This is a divergence from
general purpose programming languages to more accurately represent the EVM execution environment.

- `&s` persistent storage
- `&t` transient storage
- `&m` memory
- `&cd` calldata
- `&rd` returndata
- `&ic` internal (local) code
- `&ec` external code

### Semantics

#### Persistent and Transient Storage

Both persistent and transient storage data locations behave in similar ways, as a 256 bit map of
keys and values. The pointer itself is a 256 bit key.

Assignment to a storage pointer will set the value at the key to the value of the pointer.

#### Memory

Memory is a mutable data buffer that may be read, written, and copied. The pointer itself is a 32
bit index.

> Note: At the time of writing, memory copying requires a staticcall to the identity precompile. It
> is rarely profitable to copy memory, however, future versions of the EVM may implement MCOPY.

#### Calldata

Calldata is a read-only data buffer that may be read or copied to memory. The pointer itself is a 32
bit index.

#### Returndata

Returndata is a read-only data buffer that may be copied to memory. The pointer itself is a 32 bit
index.

#### Internal Code

Internal, or local, bytecode, is a read-only data buffer that may be copied to memory. The pointer
may be used for copying internal code to memory or as a jump destination. The pointer itself is a 32
bit index.

#### External Code

External bytecode, is a read-only data buffer that may be copied to memory. The pointer itself is a
32 bit index and 160 bit address.

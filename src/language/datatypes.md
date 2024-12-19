# Datatypes

### Datatypes

UCLID5 supports the following base datatypes:

1. `integer`: mathematical integers
2. `boolean`: the Boolean type with two values: true and false.
3. Bit-vector types `bvW`: the family of bit-vector types parameterized by their width (W).
      
    e.g. `bv32`

4. Enumeration types using the keyword `enum`:

    e.g. `enum { a, b, c}`


These types are mapped down to their SMT equivalents. 

UCLID5 also allows definition of higher-order types: arrays, records and uninterpreted functions.

5. Arrays are defined by `field_type[index_type]` where `field_type` is the type of data members stored in the array and `index_type` is the type of the array index.

    e.g. `bv32[bv4]`

6. Records are defined using the `record` keyword followed by a list of named members.

    e.g. `record { valid : boolean, payload : bv32 }`


### Named typedefs

Named typedefs of the following form are allowed:

```uclid
type <typename> = <typedefinition>;
```

For example, `message_t` is record-type with a 32-bit payload and a boolean valid flag. And `regfile_t` is an array type with 4-bit index and 32-bit register elements.

```uclid
type message_t = record { valid : boolean, payload : bv32 };

type regfile_t = bv32[bv4];
```

These typedefs can be used when instantiating variables as described below.



### Example
The following example from illustrates several type definitions defined in the `common` module. This module can be imported into multiple other modules. 

```uclid
module common {
  // addresses 8-bit vectors
  type addr_t = bv8;
  type word_t = bv8;
  // 
  type mem_t = [addr_t]word_t;
  // CPU operation.
  type op_t   = enum { op_mov, op_add, op_sub, op_load, op_store };
}
```



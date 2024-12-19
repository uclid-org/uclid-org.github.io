# Variable Declarations

### Variables

Variables in UCLID5 are instantiated using a `var` declaration as
```uclid
	var <variable_name> : <type>;
```

### Constants

Symbolic constants are declared using the `const` keyword as
```uclid
	const <const_name> : <type>;
```
Note though that the value for the constant is not defined here.

### Uninterpreted functions

Uninterpreted functions are identified by the keyword `function`: they have a list of input types and an output type.
 Uninterpreted functions can be declared `function` declaration. These functions are typed, mapping a tuple of typed arguments to a return type. 
```uclid
	function <function_name>(<input_type_list>) : <output_type>;
```
For example, the following uninterpreted function models the mapping of an instrution to an opcode.
```uclid
	function inst2op(i : word_t) : op_t;
```

### Input and output variables

Variables can be marked with the keywords `input` and `output`, expressing the fact that these variables can be connected to/from by other external modules. Note that the `var` keyword is not used. For example:

```uclid
input my_input_1 : int
output my_output_2 : bv8
```

We describe this in greater detail in the [Modules](modules.md) page.

### Accessing values

The syntax for accessing values from these variables with higher-order types is similar to languages like C/Java. Arrays are `0` indexed and use the `[]` operator. Fields from records are accessed with `.`.  
```uclid
	var myarr : [int]bool;
	sixthval = myarr[5];

	var myrec : record { valid : bool, payload : bv32 };
	pyld = myrec.payload;
```

 Index `i` of array `arr` is accessed using the syntax `arr[i]`. Field `value` in the record `result` is accessed as `result.value`.




### Example


As an example we show an excerpt of an ALU model illustrating `input` and `output` declarations as well as internal variable (normal `var`) declarations. The excerpt is taken from [here](https://github.com/uclid-org/uclid/blob/master/examples/tutorial/ex2.1-alu.ucl).

```uclid
module main {

  // typedef cmd_t which is an enum
  type cmd_t = enum { add, sub, mov_imm };
  // typedef result_t which is a record
  type result_t = record { valid : boolean, value : bv8 };

  input  valid  : boolean;
  input  cmd    : cmd_t;
  // the ALU takes two 3-bit input values from another module
  input  r1, r2 : bv3;
  input  immed  : bv8;
  output result : result_t;
  var    regs   : [bv3]bv8;
  var    cnt    : bv8; 
  
  // ... remaining module content
}
```
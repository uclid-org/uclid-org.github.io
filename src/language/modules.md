# Modules

UCLID5 provides the the user the ability to model and verify systems in a compositional manner, through the use of modules. All the previous examples had only a single module, the `main` module. However, one can define multiple modules and *instantiate* them, allowing module features to be shared across different models as well as different low level implementations for the same high-level interfaces. We illustrate this capability through an example model for a CPU.

Consider a module `common` which consists all the type definitions for the datatypes used in the CPU module. 

```uclid
// This module declares types that are used in the rest of the model.
module common {
	// addresses are uninterpreted types.
	type addr_t = bv8;
	type word_t = bv8;
	// memory
	type mem_t = [addr_t]word_t;
	// CPU operation.
	type op_t = enum { op_mov, op_add, op_sub, op_load, op_store };
}
```

These type definitions can then be conveniently imported into other modules. For example we show the `cpu` module which loads these type definitions. This is an excerpt of the example available [here](https://github.com/uclid-org/uclid/blob/master/examples/tutorial/ex3.2-cpu.ucl).

```uclid
module cpu {
  type addr_t = common.addr_t;
  type mem_t  = common.mem_t;
  type word_t = common.word_t;
  type op_t   = common.op_t;

  // remainder of the cpu module
}
```

#### Instantiating Modules

The `cpu` module can then be *instantiated* as a part of the another module. We give an example where this is the `main` module, an excerpt from [here](https://github.com/uclid-org/uclid/blob/master/examples/tutorial/ex3.3-cpu.ucl).

```uclid
module main {
  // ...  
  // Create two instances of the CPU module.
  instance cpu_i_1 : cpu(imem : (imem));
  instance cpu_i_2 : cpu(imem : (imem));

  init {
  	// ...
  }

  next {
  	// ...
  	// Invoke CPU 1 and CPU 2.
  	next (cpu_i_1);
  	next (cpu_i_2);
  }
  // ...
}
```

The `main` module consists of two instances of the `cpu` module, called as `cpu_i_1` and `cpu_i_2`. These modules can be stepped using the `next` function as is done in the `next` block of the `main` module. 

The overall model must have a single top-level module, which by default is the `main` module. The name of the top-level module module can be provided using the command-line option `-m/--main` to UCLID5.
```
	uclid -m <name of top-level module> <files>
```

Also, the module files must be specified in the order of the dependencies. For the CPU example, `main.ucl` depends on `cpu.ucl` which in turn depends on `common.ucl`, the UCLID5 tool should be invoked as follows.

```
	uclid common.ucl cpu.ucl main.ucl
```

#### Module Concatenation

Up until now, we have only defined uniquely named modules. However, this is limiting in the following ways:
1. Modules with many definitions result in a large file.
1. Verifying modules with varying definitions is cumbersome because one would need to manually swap out the definition everytime or create multiple copies of module files with the different implementations.

Thus UCLID5 allows module concatentation (a purely syntactic feature) to alleviate these issues by simply concatenating all defined modules with the same name into one module containing all the definitions defined in each module. For example, lets say we have the following files `A_x.ucl` and `A_y.ucl`:

```uclid
// Filename: A_x.ucl
module A {
    var x: integer;
    procedure foo()
        modifies x;
    {
        x = x + 1;
    }
}
```
```uclid
// Filename: A_y.ucl
module A {
    var y: integer;
    procedure bar()
        modifies y;
    {
        y = y + 1;
    }
}
```

Then running `uclid A_x.ucl A_y.ucl` results in running the concatenated module `A`:

```uclid
module A {
    var x: integer;
    var y: integer;

    procedure foo()
        modifies x;
    {
        x = x + 1;
    }

    procedure bar()
        modifies y;
    {
        y = y + 1;
    }
}
```





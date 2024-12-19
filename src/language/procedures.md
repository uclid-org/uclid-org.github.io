# Procedures


Procedures take in a list of arguments and return a set of *named* values. The following snippet extracted from the ALU [example](https://github.com/uclid-org/uclid/blob/master/examples/tutorial/ex2.1-alu.ucl) illustrates use of procedures.


```uclid
module main {
	// global variables such as valid
	// ... other stuff

	procedure exec_cmd() returns (r : result_t)
		modifies regs;
	{
		var r1val, r2val : bv8;
		if (valid) {
		  r1val, r2val = regs[r1], regs[r2];
		  case
		    (cmd == add)     : { regs[r1] = r1val + r2val; }
		    (cmd == sub)     : { regs[r1] = r1val - r2val; }
		    (cmd == mov_imm) : { regs[r1] = immed; }
		  esac
		  r.valid, r.value = true, regs[r1];
		} else { r.valid = false; }
	}

	// ... other stuff 
}
```


#### Modifies, Requires and Ensures

Procedures have three qualifiers: `modifies`, `requires` and `ensures`. The `modifies` lists a mandatory set of (global) variables which the procedure modifies, `requires` is a (optional) set of pre-conditions that the procedure needs to satisfy at the call site and `ensures` is an (optional) set of properties that the procedure guarantees will hold.

The procedure above modifies the global variable `regs`, and hence `regs` must be declared in the `modifies` list of the procedure. UCLID5 complains if the `modifies` list is not declared correctly.

`requires` and `ensures` statements allow for verification of procedures and are optional.

Extending the example above, the following additional `requires` and `ensures` statements would require that `valid` is always true at entry to the function `exec_cmd()` and gaurantees that the result at index `r1` of `regs` changes according to the command `cmd`.

```uclid
    procedure exec_cmd() returns (r : result_t)
        modifies regs;
        requires valid;
        ensures cmd == add => regs = old(regs)[r1 -> old(regs)[r1] + old(regs)[r1]];
        ensures cmd == sub ==> regs == old(regs)[r1 -> old(regs)[r1] - old(regs)[r2]];
        ensures cmd == mov_imm ==> regs == old(regs)[r1 -> immed];
    {
        var r1val, r2val : bv8;
        if (valid) {
          r1val, r2val = regs[r1], regs[r2];
          case
            (cmd == add)     : { regs[r1] = r1val + r2val; }
            (cmd == sub)     : { regs[r1] = r1val - r2val; }
            (cmd == mov_imm) : { regs[r1] = immed; }
          esac
          r.valid, r.value = true, regs[r1];
        } else { r.valid = false; }
    }
```

Optionally, one may use the `-M` argument in UCLID5 to infer the modifies sets which computes the modified variables using the LHS assignments within the procedure and the modifies sets computed for any procedure callswithin the procedure body. Example usage: `uclid -M filename.ucl`. 

#### Calling Procedures
Procedures are called using the `call` keyword. The syntax of this is rather unconvential.

```uclid
	call (variable to store return value(s)) = <procedure_name> (argument list);
```

For example the above procedure can be called as follows.
```uclid
	call (result') = exec_cmd();
```
Note how the return value can even be stored into a primed variable (in the `next`) block. Procedure calls allow complex sequential computation to be made even in the `next` block.


#### Procedure Inlining

A procedure also contains an optional no-inline/inline qualifier which instructions the UCLID5 compiler to either inline the procedure body or use the `modifies`, `requires` and `ensures` statements as a summary of the procedure call.

Without specifying a qualifier or if `inline` is specified:
```uclid
    procedure [inline] exec_cmd() returns (r : result_t)
```
The body of the procedure is [beta-reduced](https://en.wikipedia.org/wiki/Lambda_calculus) using the arguments (if any exist) and inlined at the call site. For the example above, because there are no arguments, `call exec_cmd()` would simply be replaced with the body of the procedure.

The following is an example of using `noinline`:
```uclid
    procedure [noinline] exec_cmd() returns (r : result_t)
```
In the definition above, the `noinline` qualifier indicates to the compiler to use the specification defined by the `modifies`, `requires` and `ensures` statements instead of using the body of the procedure. In this case, the variables in the `modifies` set are havoced (new symbolic constants are created), the `requires` statements are asserted and the `ensures` statements are assumed. This effectively replaces the procedure call `call exec_cmd()` with the following:

```uclid
havoc regs;
assert valid;
assume cmd == add => regs = old(regs)[r1 -> old(regs)[r1] + old(regs)[r1]];
assume cmd == sub ==> regs == old(regs)[r1 -> old(regs)[r1] - old(regs)[r2]];
assume cmd == mov_imm ==> regs == old(regs)[r1 -> immed];
```
#### Instance Procedure Calls

<span style="color:orange">NOTE</span>: This section is experimental; instance procedure calls in the next block is not well documented. If your goal is the separately define module state and procedures, please refer to [module concatenation](modules.html).

So far, all procedure calls have been made within their defining modules. However, sometimes one  may wish to call procedures from another [instance](modules.html) defined within the module. By declaring the instance name before the name of the procedure as follow:

```uclid
module A {
    // ... other stuff
    procedure foo() {
        // ... other stuff
    }
}

module main {
    instance a: A();
    
    procedure bar() {
        call a.foo();
    }
}
```
In this example, two modules `A` and `main` are defined. An instance of `A` is instantiated in module `main` and the procedure `bar` within `main` makes an instance procedure call to `a.foo()`. This executes the procedure `foo` as if the variables of instance `a` and `foo` were defined within the `main` module.


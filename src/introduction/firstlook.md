# A first look at UCLID5

A simple UCLID5 module that computes the Fibonacci sequence is shown in the [example](https://github.com/uclid-org/uclid/blob/master/examples/tutorial/ex1.1-fib-model.ucl) shown below for reference.

```uclid
module main {
    // Part 1: System description.
    var a, b : integer;
    
    init {
        a = 0;
        b = 1;
    }

    next {
        a’, b’ = b, a + b;
    }

    // Part 2: System specification.
    invariant a_le_b: a <= b;

    // Part 3: Proof script.
    control {
        unroll (3);
        check;
        print_results;
    }
}
```

We will now walk through each line in this model to understand the basics of UCLID5. The top-level syntactic structure in UCLID5 is a module. All modeling, verification and synthesis code in UCLID5 is contained within modules. In the example, we have defined one module named `main`.

The module can be conceptually split into three parts: a system description, a specification and proof script. In the example, these three conceptual parts are also kept separate in the code. The following subsections describe each of these sections.


### The System Model
This part of a UCLID5 module describes the functionality of the transition system that is being modeled: it tells us what the system does.
The first item of interest within the module main are state variables.
These are declared using the var keyword.

The module main declares two state variables: `a` and `b`. These are both of type `integer`, which corresponds to mathematical integers.
The `init` block appears next, defining the initial values of the state variables in the module. After the init block is executed, `a` and `b` have the values 0 and 1 respectively.
The `next` block appears after this and it defines the transition relation of the module. Primed variables (notation `a'`) refer to the value of the state variables at the end of the current "step". The `next` block assigns `a` to the (old) value of `b`, while `b` is assigned to the (old) value of `a + b`.

### The System Specification 
The specification answers the question: what is the system supposed to do?. In our example, we have a single *invariant* that comprises that entire specification. It is named `a_le_b` and as the name suggests, it states that `a` must be less than or equal to `b` for every reachable state of the system. Note that the keywords *invariant* and *property* are synonyms in UCLID5.

### The Proof Script
The third and final part of the UCLID5 module is a set of commands to the UCLID5 verification engine. These tell how UCLID5 should go about proving that the system satisfies its specification.

The proof script is contained within the `control` block. The commands here execute the system for 3 steps and check whether all of the systems properties (in this case, we only have one invariant: `a_le_b`) are satisfied for each of these steps. The command `unroll` executes the system for 3 steps. This execution generates four proof obligations. These proof obligations ask whether the system satisfies the invariant `a_le_b` in the initial state and in each of the 3 states reached next. The `check` command checks whether these proof obligations are satisfied and the print results prints out the results of these checks.

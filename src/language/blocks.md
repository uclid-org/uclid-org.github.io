# init, next, control

### `init` block

The `init` block initializes the variables used in the UCLID5 module. It consists of sequential assignments.

```uclid
init {
	x = 1; y = 1;
}
```

### `next` block

The `next` block models the transition relation of the module. 

```uclid
next {
	// simple parallel assignments
	x' = 1; y' = 0;

	// use of procedures to handle complex assignments
	call (z') = complex_procedure (x, y);

	// transition submodules
	next (a_submodule);
}
```

All assignments in the `next` block must be parallel assignments.
However, there are cases when complex sequential logic is required to determine the next value of a variable, which is very tedious to implement solely using parallel updates. This can then implemented by performing procedure calls from the `next` block with the procedure housing the sequential logic.

As is described in the [modules.md](Modules) section, UCLID5 modules can house other submodules, which have their own transition systems (and `next` blocks). In such cases simultaneous transition of the submodule instance `a_submodule` can be achieved by using the `next` function on `a_submodule`. 

### `control` block

The control block is essentially a verification script containing verification actions that are to be performed on the module. 

```uclid
control {
    unroll (3);
    check;
    print_results;
}
```


The control block should only be a part of the top-level module also termed as the `main` module. Typical verification actions, which have been described in the introductory examples are `unroll (k)` (unroll the transition system for `k` steps), `check` (perform verification of properties on the unrolled transition system), etc. The resutlts from the verification can be printed using the `print_results` command. We will disucss more verification strategies in the section on verification.

# Tooling

## Options
UCLID5 supports multiple command-line options, which include the following.

```shell
-m, --main <Module>                 Name of the main module.
-s, --solver <Cmd>                  Command line to invoke external SMT solver binary.
-y, --synthesizer <Cmd>             Command line to invoke SyGuS synthesizer.
-g, --smt-file-generation <value>   File prefix to generate smt files for each assertion.
-X, --exception-stack-trace         Print exception stack trace.
-f, --sygus-format                  (deprecated, enabled by default)
                                    Generate the standard SyGuS format.
-l, --llama-format                  Generates synthesis format for llama.
-e, --enum-to-numeric               Enable conversion from EnumType to NumericType
-M, --mod-set-analysis              Infers modifies set automatically.
-u, --uf-to-array                   Enable conversion from Uninterpreted Functions to Arrays.
-w, --verbosity <value>             Set verbosity level (0-4)
```

## Examples

### Specify main module (-m)
Suppose we have a file `model.ucl` with multiple modules:
```uclid
module A {
    ...
    property p_a : ...;
    control {
        v = bmc(5);
        check;
        print_results;
    }
}
module B {
    ...
    property p_b : ...;
    control {
        v = verify(...);
        check;
        print_results;
    }
}
module main {
    instance a : A();
    instance b : B();
    ...
    property p_main : ...;
    control {
        v = induction;
        check;
        print_results;
    }
}
```
UCLID5 requires a top-level module and proves properties declared in that module.
By default, UCLID5 looks for a module named `main` and uses it as the top-level module.
In this case, running `uclid model.ucl` signals UCLID5 to execute the control block
in `main` and prove property `p_main`. If we want to prove properties in module
`A` or `B`, we need to specify a top-level module other than `main`. The `-m` flag
is used for this purpose. For example,
```shell
uclid model.ucl -m A
```
selects `A` as the top-level module and UCLID5 will attempt to prove property `p_a`
using `bmc`.

### Invoke external SMT solver binary (-s)
UCLID5 supports multiple backend SMT solvers via the SMTLIB2 interface. By default,
UCLID5 uses the Z3 solver. To invoke an alternative solver, such as CVC4, we can use
the `-s` option. For example,
```shell
uclid <file.ucl> -s cvc4
```

// ### Invoke SyGuS synthesizer

### Generate SMT files for each assertion (-g)
Using the `-g` flag, UCLID5 can generate`.smt` files which contain the SMT formulae
for each property declared. `-g` also expects a prefix for the filenames. For example,
```shell
uclid <file.ucl> -g "debug"
```
generates SMT files with filenames with a pattern of `debug-xxx.smt` in the directory
where the command is executed.

# Statements and Imperative Constructs

UCLID5 allows for the following constructs:

- [Assignments](#assignments)
- [Defines](#defines)
- [For loops](#for-loops)
- [If then else](#if-then-else)
- [Case statements](#case-statements)


### Assignments

Computation in UCLID5 can be either procedural (sequential) or parallel (concurrent). Procedural computation is performed either in the init block or by defining a procedure. Parallel computation occurs in the next block.

#### Comparing Parallel vs. Sequential Assignments
Sequential assignments are of the form:
```uclid
// a sequential assignment
variable = expression;
```

The following sequential assignment
```uclid
  x = x + 1;
  y = x;
```
behaves exactly as a typical imperative assignment in usual programming languages. If the values at the beginning of the code snippet were `x = 0` and `y = 1`, post execution, the values will be `x = 1` and `y = 1`.


Parallel assignments must be of the form (the prime denotes parallel assignment):
```uclid
variable' = expression;
```

The parallel assignment:
```uclid
  x' = x + 1;
  y' = x;
```
behaves as if both the statements executed simultaneously. If the values of the variables were `x = 0` and `y = 1`, then after executing the values will be `x = 1`, `y = 0`.


> Assignments made in procedures and in the `init` block are sequential assignment while assignments made in the `next` block are parallel assignments.

We will discuss the init, next blocks as well as procedures in the coming sections.


#### Duplicate update check

Since parallel assignments update values simultaneously, UCLID5 checks whether the same variable is being written to twice. 

The following sequential snippet is allowed and ends with `x` having the value 6. 

```uclid
x = 1;
x = x + 2;
x = x + 3;
```

In contrast, the following sequence of parallel assignments is not allowed and will result in a compiler error.

```uclid
// Error, will not compile.
x' = 1;
x' = x + 2;
x' = x + 3;
```

Only a single parallel assignment to a state/output variable is allowed in a code block. Furthermore, since parallel assignments are computed in data-flow order, the order in which they are specified does not matter. This means that the following two snippets of code are equivalent:

```uclid
next {
    x' = x + 1;
    y' = x' + 1;
}
```

```uclid
next {
    y' = x' + 1;
    x' = x + 1;
}
```

UCLID5 determines that since `y'` depends on the value of `x'`, `x'` has to be computed first. This value is then used in the computation of `y'`. This is regardless of the order in which these assignments appear in the next block. Note also that the assignment to `x'` uses the value of the variable `x` at the beginning of the current step of the transition system (i.e., the "old" value of `x`). In contrast the assignment to `y'` uses the "new" value of `x`, which is the value of `x` at the end of this step of the transition system. It is important to think carefully about which version of a variable (`var` or `var'`) must be used in a particular assignment.

### Defines
UCLID5 also supports the definition of C-like macro expressions which identified by the `define` keyword as follows.

```uclid
define <name> (arg list) : <return types> = <expression>
```

e.g. `define double(arg : bv8) : bv8 = (arg + arg);`

Such definitions are useful, as in C, to define expressions over arguments that are instantiated in multiple places, or which help make the code more readable.



### For Loops


UCLID5 begin a verification language does not allow dynamic range (or unbounded) for loops. The range must be specified by numeric literals as follows:

```uclid
for (i : bv3) in (0bv3, 7bv3) { regs[i] = 1bv8; }
```
The loop iterates over the values between 0 and 7 (both-inclusive).

In many cases the values `0bv3` and `7bv3` are parameters of the model or are simply replicated across several locations in the code. Hence, UCLID5 also allows defining these values as macro-definitions. This requires the `for` statement to be declared with a typed iterator. For example, if the following macro definitions are at the module level,
```uclid
define begin() : bv3 = 0bv3;
define end() : bv3 = 7bv3;
```
we may alternatively write the for loop in set init state in the following way:
```uclid
for (i : bv3) in (begin(), end()) { regs[i] = 1bv8; }
```

### If then else

If-then-else statements follow usual imperative syntax.

```uclid
  if (condition_expression) {
    // then statements
  } else { 
    // else statements
  }
```

### Case statements

UCLID5 supports case statements in the style of case-switch statements in C++. The following snippet illustrates their use.

```uclid
    case
      (cmd == add)     : { regs[r1] = r1val + r2val; }
      (cmd == sub)     : { regs[r1] = r1val - r2val; }
      (cmd == mov_imm) : { regs[r1] = immed; }
    esac
```

`case` statements are delimited by `case` and `esac` and contain within them a list of boolean expressions and associated statement blocks. These expressions are evaluated in the order in which appear, and if any of them evaluate to true, the corresponding block is executed. If none of the case-expressions evaluate to true, nothing is executed. The keyword `default` can be used as a `catch-all` case like in C/C++.
# Inductive Proofs

Consider the following UCLID5 module that represents the Fibonacci sequence, and
suppose we want to perform unbounded verification for the property \\(a \leq
b\\). To do this, instead of the unroll command, we can use the induction
command.

```uclid
module main {
  var a, b : integer;

  init {
    a = 0; b = 1;
  }
  next {
    a', b' = b, a + b;
  }

  // First inductive invariant
  invariant a_le_b: a <= b;

  control {
    // The induction command.
    induction;
    check;
    print_results;
  }
}
```

When using the induction command as above, UCLID5 checks
  1) that the init block guarantees that the invariants hold and
  2) that, assuming the invariants hold for some state, taking a step of the
  transition from that state as defined in the next block guarantees the
  invariants will continue to hold.

The induction check for the example above will fail the second check: \\(a =
-1\\) and \\(b = 0\\) satisfy the specification but taking a step from that
state will result in \\(a = 0\\) and \\(b = -1\\), which does not satisfy the
specification. We can remedy this by adding another invariant to the model.

```uclid
module main {
  // ... same as above 

  invariant a_le_b: a <= b;
  invariant a_ge_0: a >= 0;
  
  // ... same as above
}
```

Now with the additional, invariant `a_ge_0` the combined invariants are
inductive, and the check passes.

## k-Induction

In the previous example, we used 1-induction to verify that the Fibonacci
sequence is increasing. UCLID5 also supports k-induction, which you can use by
providing the induction command with a positive integer argument. 

When using k-induction, UCLID5 checks
  1) that the first k steps, starting from the init block, all guarantee that the invariants hold and
  2) that, assuming the invariants hold for k consecutive states, taking one more step of the
  transition guarantees the invariants will continue to hold.

To better understand k-induction, consider the following UCLID5 module.

```uclid
module main {
  var x : integer;
  var y : integer;
  var b : boolean;

  init {
    b = true;
    x = 0;
    assume (y > 0);
  }

  next {
    b' = !b;
    if (b) {
        x' = x + 3;
    } else {
        x' = x - 2;
    }
    y' = y + x;
  }

  invariant x_ge_0: x >= 0;
  invariant y_gt_0: y > 0;

  control {
    v = induction(1);
    check;
    print_results;
  }
}
```

The 1-induction check for the module above will fail. Fortunately, updating the control block to use 2-induction will make the proof pass.

```uclid
module main {  
  // ... same as above

  control {
      v = induction(2);
      check;
      print_results;
  }  
}
```

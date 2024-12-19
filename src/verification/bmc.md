# Bounded Model Checking (BMC)

Bounded Model Checking (BMC) is a useful technique for bounded verification and bug finding.

(Note: the `unroll` command is a now deprecated command which performs bounded model checking for non-LTL properties only. `bmc` performs bounded model checking for both LTL and non-LTL properties)

The Fibonacci example (introduced in [A first look at UCLID5](/introduction/firstlook.html)) is reproduced below but now with the `bmc` command, which has the same semantics as the `unroll` command.

```uclid
{{#include ../code/fib.ucl}}
```

The `bmc` command unrolls the `next` block for 3 steps, and generates an SMT query for each step which checks whether the system can violate the property at that step.

## Checking Embedded Specifications 
To demonstrate checking embedded specifications,
we now present a variant of the above Fibonacci example.
```uclid
{{#include ../code/fib_embedded_spec.ucl}}
```
We have introduced the constant `flag` on line 4. 
```uclid
{{#include ../code/fib_embedded_spec.ucl:4}}
```
A constant holds a symbolic value that does not change during computation.
The initial value of the constant is assigned non-deterministically and
can be controlled using assumptions.
A second difference with between this and the first example is on lines 12–14, 23 and 25.

Line 12-14:
```uclid
{{#include ../code/fib_embedded_spec.ucl:12:14}}
```
Line 23:
```uclid
{{#include ../code/fib_embedded_spec.ucl:23}}
```
Line 25:
```uclid
{{#include ../code/fib_embedded_spec.ucl:25}}
```
Instead of using a module-level assumption declarations as in the first example,
we have three embedded assumptions in the set init procedure on lines 12–14,
and two embedded assertions in the next block on lines 23 and 25.

A module-level assumption is assumed to hold for the solver at every step of execution,
while an embedded assumption is assumed “instantaneously” at the location of the statement.
In particular, the assumptions on lines 12–14 tells the solver
to assume that `\\( a \leq b \\)`, `\\( a \geq 0 \\)` and `\\( b \leq 0 \\)` at the end of the `set_init` procedure.
Notice that we are not assigning specific values to a and b, instead we are asking Uclid5 to consider potential values of `a` and `b` such that `a ≤ b`, `a ≥ 0` and `b ≥ 0`.
Similarly the assertions on lines 23 and 25 are evaluated at that specific location in the
code. In particular the assertion on line 23 is only checked when `flag` is `true`,
while the assertion one line 25 is checked when `flag` is `false`.
Since `flag` is always `true` in our model, the assertion on line 25 will never fire.
In contrast, note that a module-level assertion would be evaluated after the init block
and after each execution of the next block.


Note that we use `unroll` here again because `bmc` currently ignores embedded specifications.
[This will be fixed](https://github.com/uclid-org/uclid/issues/181) in a future release.

## Checking LTL Specifications
We demonstrate checking LTL Specifications using BMC
with a UCLID5 model of an intersection with two traffic lights.

The full UCLID5 model:
```uclid
{{#include ../code/traffic_lights.ucl}}
```

Lines 3–39 define the functionality of the traffic light;
this part of the model should be familiar.
```uclid
{{#include ../code/traffic_lights.ucl:3:39}}
```
The current state of the lights are stored in the variables `light1` and `light2`,
and these switch from `red` to `green` to `yellow` and back to `red`.
The variables `step1` and `step2` can be thought of timers,
and ensure that each light stays red for three transitions,
green for two transitions and stays yellow for a single transition.

The LTL properties are on lines 41-43.
```uclid
{{#include ../code/traffic_lights.ucl:41:43}}
```
The property `always_one_red` specifies a safety property which states
that at least one of the two lights must be `red` in every particular cycle.
The notation `G(φ)` refers to the LTL globally operator,
while the notation `F(φ)` refers to the LTL eventually (future) operator.
Other supported operators include next-time: `X(φ)`, (strong-)until: `U(φ1, φ2)`
and weak-until: `W(φ1, φ2)`.
`always_one_red` is a safety property.
The property `eventually_green` is an example of liveness property,
and specifies that both lights become `green` infinitely often.
The command for bounded verification of LTL properties is the `bmc` command.
This is invoked on line 46.
```uclid
{{#include ../code/traffic_lights.ucl:46}}
```

Note that, currently, a UCLID5 model can only have one LTL property at a time.
[This will be fixed](https://github.com/uclid-org/uclid/issues/128) in a future release.
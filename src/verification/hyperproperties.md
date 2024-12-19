# Hyperproperties

UCLID5 supports the verification of hyperproperties at both the module level and procedural level.

#### Verifying Module Hyperproperties

One of the major benefits of using UCLID5 is the native support for creating instances. This allows the user to instantiate multiple instances that produce the traces in a hyperproperty. In this section, we present an intuitive way to verify hyperproperties. In the following sections, we present additional sugar-syntax for proving hyperinvariants of modules.

As an example, consider a transition system \\(A\\): \\[A = (V,I,\delta) = ( \\{(a, x) | a \in \mathbb{N}, x \in \mathbb{N} \\}, \\{(a, 0) | a \in\mathbb{N}\\}, \\{ ((a, x), (a^\prime, x+a)) | a,a^\prime,x\in\mathbb{N}\\})\\]

with system input \\(a\\), state variable \\(x\\), the set of initial states \\(I\\) and the transition relation \\(\delta\\).

A hyperproperty one may wish to prove is that the system is deterministic given that the inputs are the same, which can be specified as: \\[ \forall i\in\mathbb{N}. (\pi_1^i.a = \pi_2^i.a) \implies (\pi_1^i.x = \pi_2^i.x) \\]

where \\(\pi_1, \pi_2\\) are traces of the system \\(A\\) and \\(\pi_1^i, \pi_2^i\\) are the \\(i\\)-th states of the trace \\(\pi_1, \pi_2\\) respectively. We also write \\(\\pi_1^i.a\\) to mean the value of the variable \\(a\\) at state \\(\pi_1^i\\) and similarly for other other variable and traces.

To prove such a property, one would first define the transition system \\(A\\) like this
```uclid
{{#include ../code/a.ucl}}
```
and then create a main module which consists of the proof of the specification. The proof would consist of instantiating two traces of \\(A\\), \\(\pi_1\\) and \\(\pi_2\\), and specifying the invariant \\( \forall i\in\mathbb{N}. \pi_1^i.x = \pi_2^i.x \\). Note that the antecedent of the implication above is implicitly assumed when \\(a\\) is used as an input to both instances (and hence are always equal between the two traces).

```uclid
{{#include ../code/a_determinism.ucl}}
```
Simply run `uclid a.ucl a_determinism.ucl` to obtain the results.
```
% uclid a.ucl a_determinism.ucl
Successfully instantiated 2 module(s).
2 assertions passed.
0 assertions failed.
0 assertions indeterminate.
  PASSED -> v: induction_base [Step #0] property determinism @ a_determinism.ucl, line 9
  PASSED -> v: induction_step [Step #1] property determinism @ a_determinism.ucl, line 9
Finished execution for module: main.
```

#### Hyperinvariants
Alternatively, one may specify the hyperproperty of the module within the module itself as such:

```uclid
{{#include ../code/a_hyperinvariant.ucl}}
```

<span style="color:orange">NOTE</span>: UCLID5 only supports hyperinvariant checking for bounded model checking. To verify the module above, run `uclid -m A a_hyperinvariant.ucl` (this indicates to the solver to run the control block in module `A`, which defaults to `main` if not specified).

#### HyperAxioms

In addition to the `hyperinvariant` syntax, one can specify a `hyperaxiom`, which are assumptions over multiple traces at each step. Continuing with our running example, one may rewrite the antecedent of the specification as an axiom:

```uclid
{{#include ../code/a_hyperaxiom.ucl}}
```

<span style="color:orange">NOTE</span>: Hyperaxioms are only supported for bounded model checking.

#### Verifying Procedural Hyperproperties / Modular Product Verification

Up to this point, procedural properties have been specified using `modifies`, `requires` and `ensures` using the module variables and procedure arguments. UCLID5 also supports the verification of hyperproperties of procedural code.

```uclid
{{#include ../code/procedural_hyperproperty.ucl}}
```

The example above proves that the isGreaterThanOrEqual to indicator function is deterministic based on the inputs specified by line 5:
```uclid
{{#include ../code/procedural_hyperproperty.ucl:5}}
```
Similar to the hyperinvariant syntax, the variable of the \\(i\\)-th trace is referred to using the `.i` operator, where `i` is the trace number.

<span style="color:orange">NOTE</span>: As of current, procedural hyperproperties only support the verification of pure functions that do not modify module state.

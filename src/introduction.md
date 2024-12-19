# Introduction

UCLID5 is a software toolkit for the formal modeling, specification, verification, and synthesis of computational systems. The UCLID5 toolchain aims to:
1. Enable compositional (modular) modeling of finite and infinite state transition systems across a range of concurrency models and background logical theories;
2. Verification of a range of properties, including assertions, invariants, and temporal properties, and
3. Integrate modeling and verification with algorithmic and inductive synthesis.
   
UCLID5 draws inspiration from the earlier UCLID system for modeling and verification of systems {{#cite bryant-cav02}} {{#cite lahiri-cav04}}, in particular the idea of modeling concurrent systems in first-order logic with a range of background theories, and the use of proof scripts within the model. However, the UCLID5 modeling language and verification capabilities go beyond the original modeling language, and the planned integration with synthesis is novel.

This document serves as introduction to the UCLID5 modeling language and toolchain. With the UCLID5 system under active development, we expect this document to undergo several changes as the system and its applications evolve.

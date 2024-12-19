# Prerequisites

UCLID5 has two prerequisites.

1. UCLID5 requires that the JavaTM Runtime Environment be installed on your machine. You can download the latest Java Runtime Environment for your platform from [https://www.java.com](https://www.java.com).
2. UCLID5 uses the Z3 SMT solver. You can install Z3 from: [https://github.com/Z3Prover/z3/releases](https://github.com/Z3Prover/z3/releases). Make sure the `z3` or `z3.exe` binary is in your path after Z3 installed. Also make sure, the shared libraries for `libz3` and `libz3java` are in the dynamic library load path (`LD_LIBRARY_PATH` on Unix-like systems).
UCLID5 has been tested with JavaTM SE Runtime Environment version 1.8.0 and Z3 versions 4.5.1 and 4.6.0.


TODO: add optional prerequisites (CVC4, other verif. backends?).
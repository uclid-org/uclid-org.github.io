# Installation Guide

Public releases of the UCLID5 can be obtained at: [https://github.com/uclid-org/uclid/releases](https://github.com/uclid-org/uclid/releases). 

### Quick

For the impatient, the short version of the installation instructions
is: download the archive with the latest release, unzip the archive and add the `bin/` subdirectory to your `PATH`. More detailed instructions for installation are as follows.


### Detailed Installation

First, down the platform independent package from [https://github.com/uclid-org/uclid/releases](https://github.com/uclid-org/uclid/releases).
Next, follow these instructions which are provided for the bash shell running on a Unix-like platform. Operations for Micosoft Windows, or a different shell should be similar.

- Unzip the archive.   
    
    `> unzip uclid-0.9.5.zip`
- Add the uclid binary to your path.
    
    `> export PATH=$PATH:$PWD/uclid-0.9.5/bin/`

- Check that the uclid works.
    
    `> uclid --help`

This should produce output similar to the following.

```
uclid 0.9.5
Usage: uclid [options] <file> ...

  -m, --main <Module>      Name of the main module.
  -s, --solver <Cmd>       External SMT solver binary.
  -y, --synthesizer <Cmd>  Command line to invoke SyGuS synthesizer.
  -g, --smt-file-generation <value>
                           File prefix to generate smt files for each assertion.
  -X, --exception-stack-trace
                           Print exception stack trace.
  -f, --sygus-format       Generate the standard SyGuS format.
  -e, --enum-to-numeric    Enable conversion from EnumType to NumericType.
  -u, --uf-to-array        Enable conversion from Uninterpreted Functions to Arrays.
  -t, --test-fixedpoint    Test fixed point
  --help                   prints this usage text
  <file> ...               List of files to analyze.
```

### Example

Invoke UCLID5 on a model is easy. Just run the uclid binary and provide a list of files containing the model as a command-line argument. When invoked, UCLID5 will parse each of these files and look for a module named main among them. It will execute the commands in the main module’s control block. The `--main` command line argument can be used to specify a different name for the `main` module. Note only the `main` module's control blocks will be executed, even if the main module instantiates other modules with control blocks. If no main module is found, UCLID5 will exit with an
error, as we saw in the previous section when uclid was invoked without arguments. 

Example 1.1 is part of the UCLID5 distribution in the `examples/tutorial/` subdirectory. You can run UCLID5 on this model as:

`  >  uclid examples/tutorial/ex1.1-fib-model.ucl`

This should produce the following output.

```
Successfully parsed 1 and instantiated 1 module(s).
4 assertions passed.
0 assertions failed.
0 assertions indeterminate.
Finished execution for module: main.
```
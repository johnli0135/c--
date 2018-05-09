# C--

`c--` is a bash script that repeatedly runs the C/C++ preprocessor `cpp` on a given
input file until the output stops changing.

Usage: `c-- <filename> --verbosity=n`

Verbosity levels:
* `--verbosity=0`: only output the final result
* `--verbosity=1`: also output the result of intermediate passes
* `--verbosity=2`: also output any errors/warnings emitted by `cpp`

## Computing with tuples

The [tuple approach](https://github.com/johnli0135/c--/blob/master/tuple/) uses parameter lists
as tuples to represent data and parametrized macros to represent functions.
(e.g. `f(g(x))` is written `f g x`.)

[prelude.h](https://github.com/johnli0135/c--/blob/master/tuple/prelude.h) defines some useful constructs
like stacks/lists, natural numbers, arithmetic, predicates, and conditional execution.

[examples.cmm](https://github.com/johnli0135/c--/blob/master/tuple/examples.cmm) contains some sample code.

## Computing with low level constructs

The [lowlevel approach](https://github.com/johnli0135/c--/blob/master/lowlevel/) uses lots of variables
to maintain a virtual machine that includes an instruction pointer, a temporary register, and RAM.

[make.py](https://github.com/johnli0135/c--/blob/master/lowlevel/make.py) generates, given a fixed word size
and memory size, preprocessor code responsible for maintaining the state of the virtual machine and loading
and executing instructions from memory.

The generated files (in particular, [vm.h](https://github.com/johnli0135/c--/blob/master/lowlevel/vm.h)) include comments describing the implementation in more detail.

Programs can be written by directly modifying the initial memory layout. This can be done by including a modified
copy of [init_template.h](https://github.com/johnli0135/c--/blob/master/lowlevel/init_template.h) with the proper instructions
filled in. For example, the following initialization, designed for a VM with word size 4 and memory size 3,
will store the string `1111` in memory address `1` (i.e., `MEM_4` .. `MEM_7`) and then hang indefinitely:

```C
/*
  This file defines the initial state of the VM.

  Programs are written by setting values in "main memory" to
  instruction codes and their arguments.
*/

// programs start at address 0
#define IP_0000 0000
#define IP_0001 0001
#define IP_0010 0010
#define IP_0011 0011
#define AP_0000 0100
#define AP_0001 0101
#define AP_0010 0110
#define AP_0011 0111

// initialize temp register
#define TMP_0000 1
#define TMP_0001 1
#define TMP_0010 1
#define TMP_0011 1

// initialize main memory
#define MEM_0000 STORE_0
#define MEM_0001 STORE_1
#define MEM_0010 STORE_2
#define MEM_0011 STORE_3
#define MEM_0100 0
#define MEM_0101 1
#define MEM_0110 0
#define MEM_0111 0
#define MEM_1000 JMP_0
#define MEM_1001 JMP_1
#define MEM_1010 JMP_2
#define MEM_1011 JMP_3
#define MEM_1100 0
#define MEM_1101 0
#define MEM_1110 0
#define MEM_1111 0
#define MEM_10000 0
#define MEM_10001 0
#define MEM_10010 0
#define MEM_10011 0
```

The program can be run by including this and other header files generated by make.py
in the right order and passing the resulting file to `c--`:

```C
#include "consts.h"
#include "init_store1111.h"
#include "vm.h"
```


# Reverse Engineering

*Sources: Recon2024*

## Index
- [techniques](#techniques)
- [interesting tools](#interesting-tools)
- [other tech](#other-tech)


# Techniques

## Code Optimization

Basic techniques used in code optimization can help with obfuscation removal.
Things like junk code are trivial to remove using llvm.

### Dead code elimination

Remove instructions that have no impact on the output.
Be wary of flags and other side effects

```asm
; 1
and eax, eax

; 2
push eax
add al, 0x5
pop eax
```

### Constant folding

Combine instructions that equate to constants into one

```asm
add eax, 0x5
mul eax, 0x2
add eax, 0x2

; becomes
add eax, 0xc
```

### Constant propagation

Replace constants by their values when possible
```asm
xor eax, eax
add eax, 0x5
mov edx, eax

;becomes
add edx, 0x5
```

## Obfuscation Techniques

Junk code, instruction substitution and constant unfolding are basic examples of these.

### Opaque Predicates

Introduce conditionals which evalutate always one way, but introduce fake complexity to a control flow graph.
Hampers static analysis.

### Control Flow Flattening

Reorder basic blocks into a flat state machine like structure, using a set (usually at end of block) state to tell the dispatcher where is the next block.
Complicates static analysis.

### VM based obfuscation

Somewhat an extension of control flow flattening, extends the idea of a dispatcher to an interpreter.
Translate code into custom instruction set and run it in vm.
Can get pretty complex, and complicates static and dynamic analysis if done right.

### MBAs

These are boolean expressions which are equivalent. Goal is to replace simple ones with arbitrarily complex ones.

example:
```
x + y = (x ^ y) + 2 * (x & y)
```
This can get much more complex.

## Symbolic Execution

Given a state and code, use a symbolic engine and run the code:

Execute operations but instead of having a concrete (values) state, remember variables and their operations.
Useful to construct constraints and explore conditions of code.

### Concolic Execution

Symbolic execution but with concrete values in certain inputs to force taking a single (sometimes specific) execution path.
If want to see others, create expressions for conditionals, negate them, solve smt and force other path.

## SMT solvers

Solvers used to prove equivalence of equations/programs, or to find valid inputs to satisfy wanted condition.
Very useful, but can get computationally expensive.

[book](https://smt.st/SAT_SMT_by_example.pdf)

## Program Synthesis

Theoretical branch of computer science where the goal is to generate a program only from desired I/O.
Has applications in optimization and recently deobfuscation.

### Stochastic

Use an algorithm to generate expressions to then compare an find similar I/O to target obfuscated expression.
Slow and has problems with bigger expressions which it will never find.

[paper](https://synthesis.to/papers/msc_thesis.pdf)

### QSynthesis

Store DB of expressions, hashing their I/O behavior, then walk the expression AST, substituting same I/O recursively. Trying to substitute bigger subtrees first.

[paper](https://i.blackhat.com/USA21/Wednesday-Handouts/US-21-David-Greybox-Program-Synthesis-wp.pdf)

### Equality Saturation

Use Equality classes to expand possible ASTs and improve poor perfomance of QSynth approach when linked simplifications are in different subtress

[blogpost](https://secret.club/2022/08/08/eqsat-oracle-synthesis.html)

# Interesting tools

## [angr](https://angr.io/)

Symbolic Execution engine, and reveng framework.
Implements Concolic Execution to find values and Solutions to take execution paths.
Uses VEX as IR.

## [miasm](https://github.com/cea-sec/miasm)

Reverse engineering framework in python, works well with Symbolic Execution and SMT solvers.
Custom IR that is more flexible than angr? IR is side effects free.

## [msynth](https://github.com/mrphrazer/msynth)

Program synthesis (QSynthesis) framework in python

## [z3](https://github.com/Z3Prover/z3)

Probably best (most extensive not fastest necessarily) SMT solver. Has nice Python API.

## [rev.ng](https://rev.ng/)

(New) Decompiler and Reverse Engineering framework. Nice clean declarative workflow.
ASM -> QEMU TinyCode -> LLVM IR -> Decompile

Let's you work on llvm ir level or decompiled C level. Syntactically correct C.

## [nyxstone](https://github.com/emproof-com/nyxstone)

Assembling/Disassembling framework (on top of llvm). Good for editing on a binary level.
Has Rust, C++ and Python bindings.

## [klee](https://klee-se.org/)

Symbolic execution engine for symbolic fuzzing.
Works on top of llvm IR. Can generate converage data live.

## [Unicorn](https://github.com/unicorn-engine/unicorn)

CPU emulator

## [Qiling](https://github.com/qilingframework/qiling)

Binary emulation framewok on top of Unicorn

## Android RE

### [jadx](https://github.com/skylot/jadx)

Decompiler and project explorer for Android RE (APK, Dexfiles)

### [Katalina](https://github.com/huuck/Katalina)

Emulation engine for Android bc (think Unicorn but Dalvik bytecode)

## [frida](https://frida.re/)

# other tech

## IRs

Intermediate representations are languages/code that are often an in between step in compilation, decompilation or translation pipelines.
They are useful to represent code in a way that is more adapted to the task at hand. Or to generalize code from multiple sources (llvm).

### LLVM IR

[llvm](https://llvm.org/) has its own intermediate representation. It is extensively used in a lot of use cases, not only in compilation.
Anything translated to llvm ir can leverage the power of the llvm api, backends...

### VEX (Valgrind) IR

Side effects free IR. Means all instructions modify only what is in them. Which means we get a lot of operations on flag registers usually (as those happen often on common instructions).

## Obfuscators

There are 3 ways to do obfuscation:
- source-level
- compiler
- binary-level

### OLLVM

LLVM based obfuscation, including instruction substitution, junk code, control flow flattening and mba insertion.

[github](https://github.com/obfuscator-llvm/obfuscator)

### Themida

Commercial, binary level, code protection solution. Provides also VM based obfuscation.

### VMProtect

Similar to Themida.

[blogpost](https://blog.back.engineering/17/05/2021/)

### Tigress

Academic, source-level, vm obfuscator.

[website](https://tigress.wtf/introduction.html)

### Loki

Another vm obfuscator prototype implementing novel and more resilient approaches.

[video](https://www.youtube.com/watch?v=EWCcOlJf4pE)
[paper](https://synthesis.to/papers/usenix22-loki.pdf)  


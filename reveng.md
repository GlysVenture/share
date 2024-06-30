# Reverse Engineering

*Sources: Recon2024*

## Index
- [techniques](#techniques)
- [interesting tools](#interesting-tools)
- [other tech](#other-tech)


# Techniques

## Code Optimization

Basic techniques used in code optimizationcan help with obfuscation removal.
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

## Symbolic Execution



### Concolic Execution

Symbolic execution but with concrete values in inputs so to take a certain path.
If want to see others, create expressions for conditionals, negate them, solve smt and force other path.

## SMT solvers

## Program Synthesis

### Stochastic 

### QSynthesis

### Equality Saturation

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

### LLVM IR

### VEX (Valgrind) IR

## Obfuscators

### OLLVM

### Themida

### VMProtect

### Tigress

### Loki

##  


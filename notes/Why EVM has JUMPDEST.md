# Why EVM has JUMPDEST

### Executive Summary

JUMPDEST is a way to make EVM feasible for bytecode-compilation (enabling faster JIT or AOT execution) and to avoid certain security risks, which systems like x86 face (code obfuscation, polymorphic code and partially return-oriented programming attacks).

### History

The original EVM design from 2014 didn't have JUMPDEST. It was the minimal change introduced to make the [EVMJIT](https://github.com/ethereum/evmjit) project at least feasible.

See the following Yellow Paper changes:
- [Jump destinations defined.](https://github.com/ethereum/yellowpaper/commit/e6318462bb54c5ed5e8c727bfaa90b2dc9d6a7d4)
- [JUMPDESTs must be used.](https://github.com/ethereum/yellowpaper/commit/b490cdb014fa2d67d942660f526cf51a5e43ea58)

### EVM bytecode compilation

Virtual Machines are always written as a middle solution, and are either interpreted, ahead-of-time compiled (AOT), or Just-in-time compiled (JIT). 

Without JUMPDEST we can still interpret the VM, but both compilation modes would be exceedingly non-linear even in the average case. In the example of LLVM IR, the compiler has to split the code into [basic blocks](https://en.wikipedia.org/wiki/Basic_block). For "static" jumps (e.g. `PUSH`+`JUMP`) this is easy because the jump target can be deduced during compilation. For "dynamic" jumps the compiler still needs to know the set of potential targets. You can check how splitting into basic blocks is done in:
- [EVMJIT's Compiler.cpp](https://github.com/ethereum/evmjit/blob/master/libevmjit/Compiler.cpp)
- [revmc's sections.rs](https://github.com/paradigmxyz/revmc/blob/main/crates/revmc/src/bytecode/sections.rs)

Note that although "static" `PUSH`+`JUMP` pattern is very common, "dynamic" jumps are ubiquitous as well, because compilers implement returns from functions this way.

Without JUMPDEST, every single byte of the code (including PUSH data and any other "data" like the metadata hash) is a potential target. There is no other way than put every instruction (and data bytes) into separate basic block.

With JUMPDEST, there is one global set of potential jump targets. The situation becomes feasible, but far from optimal: better approach is to define a set of targets per "dynamic" jump instruction as this is done e.g. in LLVM IR's [`indirectbr`](https://llvm.org/docs/LangRef.html#indirectbr-instruction).

Adding JUMPDEST made the analysis more tractable for ahead of time compilation. However to get reliable JIT compilation we need linear time compilation, and for that we need to ban dynamic jumps as well as enforce stack validation.

### Static analysis

Similarly to compilation, the same properties are needed for code analysis and formal verification. JUMPDEST makes them feasible, but far from easy.

You can further read about static analysis and formal verification in the context of EVM and JUMPDEST:

- [MadMax: surviving out-of-gas conditions in Ethereum smart contracts](https://dl.acm.org/doi/10.1145/3276486) (Grech et al., 2018)

   > - Detecting basic blocks is trivial (because of explicit jump labels), but identifying their connectivity to form a control-flow graph (i.e., the possible targets of every jump) requires a value-flow analysis. The same is true for detecting possible values (or types) of arguments for every operation, that is, the shape of the stack (i.e., its size and static types at every position).
   > - Detecting function boundaries requires a high-fidelity analysis. Common patterns in the compilation of contract code exacerbate this, e.g., in the Solidity compiler, since most smart contracts are written in Solidity. The Solidity compiler translates local contract calls into jumps to a _dispatcher_ routine, which then redirects the call to the initial basic block of the target function.

- [EIP-3779: Safer Control Flow for the EVM](https://eips.ethereum.org/EIPS/eip-3779) (Colvin, Zelenka, 2021)
  
  > For our purposes we define a safe EVM contract as one that cannot encounter an exceptional halting state. From the standpoint of security it would be best if unsafe contracts were never placed on the blockchain. Unsafe code can attempt to overflow stack, underflow stack, execute invalid instructions, and jump to invalid locations.

### Security

JUMPDESTs prevent data execution in EVM. This is also appreciated from the security point of view because prevents techniques like:
- [code obfuscation](https://en.wikipedia.org/wiki/Obfuscation_(software)),
- [polymorphic code](https://en.wikipedia.org/wiki/Polymorphic_code),
- exploits that rely on jumping to unintended locations.

Other systems, like x86, allow jumping into code directly, which makes them susceptible to the above problems. An attempt to x86 to allievate this has been the [NX-bit](https://en.wikipedia.org/wiki/NX_bit).

### Further reading

- ["The Good, the Bad and the Ugly:
Pitfalls and Best Practices in Automated Sound
Static Analysis of Ethereum Smart Contracts" (Schneidewind et al., 2021)](https://arxiv.org/pdf/2101.05735)
- [Control Flow Graph reconstruction for EVM bytecode
](https://hackmd.io/@FranckC/rJIRA43Na)
- [Pawel's talk about jumps in EVM and comparison with other IRs](https://www.youtube.com/watch?v=8Cp8IsmIJl4)
- ["KEVM: A Complete Semantics of the Ethereum Virtual Machine" (Hildenbrandt et al., 2018)](https://www.ideals.illinois.edu/items/107147)
   - Discusses formal verification benefits of structured JUMPDEST validation
- ["Sereum: Protecting Existing Smart Contracts Against Re-Entrancy Attacks" (Rodler et al., 2019)](https://ieeexplore.ieee.org/document/8835275)
   - Notes how JUMPDEST validation helps in taint analysis and security verification
- [Hari on "Stack too deep"](https://x.com/_hrkrshnn/status/1900316094725845126)
# EVMMAX
## Sections:
1. Why? 
    1. Enable convenient way to implement cryptography related functions in EVM
    2. No need to add new precompiles in the future. 
    3. No need to wait until requested precompile is on mainnet.
    4. To avoid implementation specific details problem as we have now when adding a new precompile.  To mitigate precompile implementation specific consensus issues.
    5. To allow ZK and others to deliver specialized cryptography libraries available in EVM (in yul, solidity, viper languages, etc.)
2. What is EVMMAX?
    1. EVM Modular Arithmetic Extension. Name explanation.
    2. It implements prime field operations (now) in EVM. Next step odd and 2^n field operation.
    3. Where it is in the architecture (nice diagram showing the layers.)
    4. The EIP link. 
    5. Future evolution of EOF
3. Issue with current solution (precompiles):
    1. (probably 2-3 slides) ECC bn254 point addition. nice diagram explaining what the precompile does. 
        1. How it's used. It's supposed to be a tool to implement advanced ECC algorithms on chain. 
        2. Problem
            1. Most of the computation time spent in inversion. 90% of time.
            2. It's not need because we want to process the result farther.  
            3. We want to stay in projection or Jacobian coordinates. No need to go to affine and back.
            4. The bn254 point addition precompile is useless in this case.
        3.  Solution. We can stay in chosen coordinates system and convert to affine if needed. If ever.
    2.  ECC bn254 point multiplication - to be considered as a example here. [ecmul](https://github.com/ethereum/evmone/commit/ca89d0d5d3baae819edbfe5cc309a7c4a60e1693#diff-f0580497a0a89a462762262bcfdd6529aea863dc0d88e973eff0613cea613a62R96)
4. Review of the current spec
    1. Opcodes (Similar to EVM Summit part but shorter)
        1. `ADDMODX`, `SUBMODX`, `MULMODX` 
        2. `SETUPX` (Setup EVMMAX context, define `MODULUS`, number of available slots, switch context)
        3. `STOREX`, `LOADX` (to/from Montgomery form conversion) 
5. Current state
    6. EVMMAX opcode in evmone (simple EVMMAX bytecode generation support)
    7. ecadd, ecmul (for bn curve ) implemented and benchmarked
7. What to do next.
    1. Implement bls precompiles counterparts in EVMMAX.
    2. Support odd and 2^k order fields. Go beyond ECC targeted solution.
    3. Implement hashing functions based on this (Poseidon hash function i.e.)
    4. Redesign if needed. I.e. better support for control flow, new opcodes (`EQX` or `IS_ZEROX`), enable better static analysis of the EVMMAX bytecode.
    5. Research SIMD EVMMAX version on AVX (Advanced Vector Extension) usability and support for vectorized inputs.
    6. Support EVMMAX in higher level languages like yul, solidity or Huff.
    7. Make EVMMAX a spec language to define precompiles if they will be still needed. R&D on possible transpiler from EVMMAX to high level languages (i.e. Go, C++, JS, Java, Rust.) it would potentially enable EL clients to use the reference implementation of precompiles in EVMMAX as libraries in any language they need.
# EVMMAX in evmone

## Context

- [EIP6690](https://github.com/jwasinger/EIPs/blob/evmmax-no-eof/EIPS/eip-6690.md)

Current implementation in [PR742](https://github.com/ethereum/evmone/pull/742) is simplified. Changing context is allowed but switching back to the previous one is not. On `SETUPX` existing context is removed.


## Purpose

- To allow anybody to implement their `ecc` related functions in `EVM`.
- To implement optimized precompiles in `evmmax`.
- Gather conclusions and feedback on `evmmax` specification. Extend or change the spec if needed.
- Use precompiled contract implemented in `evmmax` as a benchmark.
- Use future precompiles implementation in `evmmax` as a reference implementation for precompiles.
- If possible avoid adding new precompiled contracts and use `evmmax` instead.

## Already DONE

- `evmmax` opcode support in evmone. (in review) [PR742](https://github.com/ethereum/evmone/pull/742)
- `ecmul` and `ecadd` implemented using `evmmax` bytecode. [evmmax-evm-precompiles commit](https://github.com/ethereum/evmone/commit/ca89d0d5d3baae819edbfe5cc309a7c4a60e1693) (feedback below)
- optimized `ecparing` implementation DONE (in review) [PR852](https://github.com/ethereum/evmone/pull/852). Now can be translated into `evmmax` bytecode. 


## Conclusions

From `ecmul`, `ecadd` as `evmmax` bytecodes.

- Very often we need to compare values in two slots or compute negation. In these cases we need to store `0` value in a slot. It would be good to have it pre-initialized and i.e. read-only slot with this value. 
- Similarly but because of a different reason we need slot initialized to `1` to avoid gas expensive operations initializing slots to `1`. This is particularly useful when converting affine points into projective or Jacobian coordinates. We need just to add new coordinate or coordinates initialized to `1`.
- Very useful additional feature would be fast values in slot comparison and return the result on evm stack. It will allow us to change control flow according to returned value without expensive `loadx` opcode.

From optimized `ecpairing`
- Most of the algorithms analyzed till now are based on scalar binary representation. I.e. `ecparing`, `ecmul` etc. It would be very beneficial to avoid scalar bit manipulation in evm context. Not sure yet if it's possible.
- There is a couple of constants being used by `ecparing`. It would be good to have them pre-initialized to avoid expensive initialization on every call. Rough idea is to load them in data section on `SETUPX` I.e.
    - coefficients used to compute Frobenius endomorphism in optimized way
    - extension fields `ksi` coefficients
    - twisted curve coefficients used to implement faster extended field arithmetic.

General
- It's not possible in general to verify `evmmax` opcodes during EOF validation. It has to be validated in runtime. The reason is that in general we don't know if and what `SETUPX` opcode was executed. There are a couple of ideas how is can be redesigned to allow static validation.
    - TODO
     

## What's next?

- Analyze and address feedback from the previous section. Make a research and analysis of requirements from L2s. 
- Update evmmax spec.
- Revisit gas schedule of evmmax opcodes.
- Implement evmmax opcodes support in [Huff](https://docs.huff.sh/) to add convenient way to implement more advanced ecc functionality.
- Implement ecpairing for bn254 curve in evmmax bytecode. Benchmark against other implementations.
- Implement "point evaluation" precompile in evmmax.
- Implement bls128 curve precompiles in evmmax.


###### tags: `evmmax`
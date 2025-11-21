# EOF - Solidity library compatibility (initial) report 

This document describes EOF's impact on the most popular existing libraries. We prepared prototypes of changes required to compile their source code. We describe unresolved issues and propose possible solutions. "Required changes to the source code" does not contain unresolved issues but sometimes contains TODO comments added to the source code.

ⓘ Note: The prototypes make use of local paths in configuration and depend on a local build of the experimental Solidity compiler [branch enabling block deduplicator on EOF](https://github.com/ethereum/solidity/pull/15993) rebased on the [parallel branch enabling the constant optimizer](https://github.com/ethereum/solidity/pull/15985).

The first section addresses issues found in specific projects, and is followed by a categorical list of the kinds of changes authors will need to make.

This document is based on the EOF scope approved in March of 2025. However, based on the findings of this report and the community feedback aroud these changes, Team Ipsilon recommends changes to the spec to reflect ["Option D - Introspecting EOF"](https://notes.ethereum.org/@ipsilon/eof_fusaka_options). The adoption of this option will remove most of the required code changes listed in this report.

## Uniswap V4 (core)

### [Required changes to the source code:](https://github.com/ipsilon/v4-core/commit/ebab60449e634e05f2ab60143b4cd4e0564fe74f)
`3 files +9 -6 lines changed`

- Changes to project metadata such as changing the solc version and binary, updating the target VM, and enabling EOF.
- Updating 3 assembly sections using `call()` to use `extcall()` instead and adjust to new return semantics. All calls pass in all available gas.
    - Some of the calls are pure value transfers so they can be replaced with `PAY` opcode, simplifying the code base. 
- Adding 1 instance of copying data from the return buffer into memory. 

### Remaining issue:

[`test/utils/V3Helper.sol:43`](https://github.com/ipsilon/v4-core/blob/35edd8eda629b4e6eedb486e7f6e5cb74a8e4071/test/utils/V3Helper.sol#L43) 

This issue is present only in project's test suite and is actually an issue of a dependency (`forge-std`), whose compatibility is explored in more detail in a later section.

Possible solutions:
1. Introduce a cheat for deploying bytecode from a string. This is test code and it already depends on the `vm.getCode()` cheat that does things beyond what a real EVM allows. Introducing a more powerful cheat seems in line with this approach.
2. Deploy this bytecode from a legacy contract. The fact that it is included as verbatim bytecode and not compiled from source indicates that the intent was to test interoperability of Uniswap v4 with v3 as it exists on chain now. For such a use case, deploying a legacy contract would actually be the right thing to do.
3. Remove `forge-std` dependency and have the framework directly deploy the contract as a part of test setup rather than require the code under test to do it.
4. Convert the contract to EOF and compile it from source, which would make it deployable using either `TXCREATE` or `EOFCREATE`.
    - The part of the framework that runs the test suite could simulate an `InitcodeTransation` and provide a list of `initcodes` accessible via hash, which then would be deployable with `TXCREATE`.
    - The bytecode could be attached as a [verbatim contract](https://github.com/ethereum/solidity/issues/16027) in Solidity and deployed using `EOFCREATE`.
5. Reintroduce code introspection.

### Impact:
Only test code is affected and the issue is in a dependency (`forge-std`).

## Uniswap V4 (periphery)

No repo because the fix was to adjust one `call` (all gas passed) in [src/base/Notifier.sol:129](https://github.com/ipsilon/v4-periphery/blob/main/src/base/Notifier.sol#L129)

Dependency: `v4-core` and `forge-std`

### Remaining issue:

#### [src/base/Notifier.sol:85](https://github.com/ipsilon/v4-periphery/blob/main/src/base/Notifier.sol#L85) _Gas introspection ban_

Possible solution:
1. Reintroduce `gas`.
2. Use middle-ground proposed solution [CALL_GAS_SPLIT](https://github.com/ethereum/EIPs/pull/9558).

## openzeppelin-contracts

In this case we used Hardhat to build the library as it doesn't depend on `forge-std`

### [Required changes to the source code:](https://github.com/ipsilon/openzeppelin-contracts/commit/421ee75aa12c309558fe53b6395f08968a4a9ca1)
`8 files changed +49 -12 lines changed`

- Changes to project metadata such as changing the solc version and binary, updating the target VM, enabling EOF, and enabling compilation via IR.
- Updates to a Hardhat subtask to use a local compiler (will go away if we use a published version).
- Updating 2 assembly sections using `call()` to use `extcall()` and adjust to new return semantics. All calls pass in all available gas.
- Updating 1 assembly section using `delegatecall()` to use `extdelegatecall()` and adjust to new return semantics. All calls pass in all available gas.
- Updating 5 assembly sections using `staticcall()` to use `extsaticcall()` and adjust to new return semantics. All calls pass in all available gas.
- Adding 7 instances of copying data from the return buffer into memory.

### Remaining issues:

#### [contracts/metatx/ERC2771Forwarder.sol](https://github.com/ipsilon/openzeppelin-contracts/blob/421ee75aa12c309558fe53b6395f08968a4a9ca1/contracts/metatx/ERC2771Forwarder.sol#L290) _Lack of gas observability._

A library that supports "meta transactions" (a precursor to EIP-4337 AA wallets).

Possible solutions:
1. Reintroduce `gas`.
2. Use middle-ground proposed solution [CALL_GAS_SPLIT](https://github.com/ethereum/EIPs/pull/9558).
3. Do not migrate to EOF as newer contracts will use EIP-4337 AA wallets or EIP-7702 native AA.

#### [contracts/utils/Create2.sol](https://github.com/ipsilon/openzeppelin-contracts/blob/0034c302241c4b1a1685272d4df42ca5d64b8c34/contracts/utils/Create2.sol#L45) _Create/Create2 helper_

Support for various `CREATE2` tasks.
 
Possible solutions:
1. Implement a `TXCREATE`-based equivalent for counterfactual deployment use-cases. It would expect the bytecode to be provided in an `InitcodeTransaction` rather than passed in via arguments.
2. Implement an `EOFCREATE`-based equivalent for deployment of predefined bytecode blobs. This would rely on the [verbatim contract](https://github.com/ethereum/solidity/issues/16027) feature to be introduced in Solidity.

#### [Clones.sol](https://github.com/ipsilon/openzeppelin-contracts/blob/d183d9b07a6cb0772ff52aa4e3e40165e99d6359/contracts/proxy/Clones.sol) _Clones_

A library that supports lightweight delegate contracts, including facilities to customize the clone with immutable data.

Possible solutions:
1. Use the `EOFCREATE`-based variant of `Create2` with a [verbatim contract](https://github.com/ethereum/solidity/issues/16027). This would be straighforward for the base case of a deterministic clone with no args. The library also provides extra varieties of the clone pattern that need additional consideration:
    1. *non-deterministic clone* (using `CREATE` rather than `CREATE2`). EOF intentionally does away with nonce-based contract deployment, so non-deterministic variant should be retired. If desired, it can still be simulated using an independently managed nonce in storage, mixed into the salt.
    2. *clone with immutable args*. Currently implemented by inserting the immutable values into the bytecode before deployment. On EOF immutables are stored in the data section by the creation code using `RETURNCONTRACT`, so this is a matter of changing the clone implementation to make use of this mechanism instead. Then the immutable values need to be passed to the initcode, which can be achieved in a variety of ways (// FIXME: needs research, talk to OpenZeppelin):
        - passing them in through calldata, as constructor arguments
        - using a notional `EXTDATACOPY` operation (no EIP)
        - functional support to extract params from the clone host
2. Include [EIP-7819: Create Delegate](https://eips.ethereum.org/EIPS/eip-7819) in a hard fork. This EIP's first author is an OpenZeppelin developer. This EIP allows contracts to create EIP-7702 delegate for contract address.

#### [ERC165Checker.sol](https://github.com/ipsilon/openzeppelin-contracts/blob/0034c302241c4b1a1685272d4df42ca5d64b8c34/contracts/utils/introspection/ERC165Checker.sol#L117) _Check interface_

A hard coded 30k gas limit is included in the ERC-165 check call, [as recommended](https://eips.ethereum.org/EIPS/eip-165#how-to-detect-if-a-contract-implements-erc-165) by the ERC.

##### Possible solutions:
1. Reintroduce `gas`.
2. Use middle-ground proposed solution [CALL_GAS_SPLIT](https://github.com/ethereum/EIPs/pull/9558).

### Impact: Low

## forge-std

Forge uses a customized EVM to provide a lot of Solidity-focused testing support. This customization is where most of the changes need to be made.

### [Required changes to the source code:](https://github.com/ipsilon/forge-std/commit/5bd4e9743bceb4a2962ca340ee0482adfa1dc67d)
`+17 -9 lines changed`

- Changes to project metadata such as changing the solc version and binary, updating the target VM, enabling EOF.
- 5 instances of using EXTCODESIZE to determine if an account is a contract.
- Updating 5 assembly sections using `staticcall()` to use `extstaticcall()` and adjust to new return semantics. All calls pass in all available gas.

### Remaining issues:

<!-- #### [src/StdCheats.sol](https://github.com/ipsilon/forge-std/blob/5bd4e9743bceb4a2962ca340ee0482adfa1dc67d/src/StdCheats.sol#L214) _Check if an address is a contract_


##### Possible solution:
1. Use `ISCONTRACT` which is going to be introduced in devnet-2


#### [test/StdCheats.t.sol](https://github.com/ipsilon/forge-std/blob/5bd4e9743bceb4a2962ca340ee0482adfa1dc67d/test/StdCheats.t.sol) SAME AS ABOVE

--> 

#### [src/StdCheats.sol](https://github.com/ipsilon/forge-std/blob/5bd4e9743bceb4a2962ca340ee0482adfa1dc67d/src/StdCheats.sol#L508) _Create a contract from bytecode_

Forge uses "cheats" for building and testing, which involves a customized VM that allows it to break the sandbox and load local system files and load them as contracts.

##### Possible solutions:

Cheat EOF bytecode into the "initcodes" portion of the transaction, or the equivalant.

1. Update the VM with custom `TXCREATE` code. In addition to looking in the `InitcodeTransaction`'s `initcodes` field, look in a standard 'pre-image' pool where contracts are indexed by hash.
2. Load the preimage pool at an appropriate time. Two good options:
  a. When loading bytecode from a file. Hash it and put it in the pre-image pool.
  b. Every time `keccak` is performed, store the result in the preimage pool. This has the added benefit of test-generated EOF code.
3. Change the [deployCode variants](https://github.com/foundry-rs/forge-std/blob/9530d9ec702df1b27b7f8f50c0a63a11b1b5fba9/src/StdCheats.sol#L502-L541) to check the bytecode for EOF's magic bytes
  a. Legacy code goes to `CREATE`
  b. EOF code goes to `TXCREATE`, but the hash id comes from the pre-image pool.
  
This preserves the magic of "cheating" in a contract from the local filesystem.

### Impact:
Low to moderate. Depends on amount of work to do in `VMSafe` interface implementation.

## solady

The nature of this library is in using low level assembly optimizations. This makes it sensitive on any EVM assembly change. Since EOF is an EVM format change, supporting it requires changes in many places in the library. Nevertheless the solady team has prepared well defined [Solady EOF](https://github.com/Vectorized/solady/issues/1142) plan.
The team estimates that they [need a month](https://x.com/optimizoor/status/1916379240460914846) to start supporting EOF. 


## Categorical Summary of Issues

### Project Metadata

All projects will require an updated version of Solidity.  As many projects lock in to a specific version of Solidity for code reproducibility reasons, this is a standard change.

### Transition to EIP-7069 calls in assembly blocks

To support the "Ban Gas Introspection" goal EOF introduces a new series of CALL instructions that do not allow gas to be limited. These instructions also implement another long-requested optimization to not require output data in memory but to just use the returndata buffer.

The adoption of "Option D - Introspecting EOF" will address all these concerns.

### Hard-coding gas values

Hard-coding gas values runs counter to the "Ban Gas Introspection" theme.

In general this is considered a bad practice as there are major gas schedule changes on the horizon for Glamsterdam, and has been an auditor's "do not" recommendation for more than 5 years. More durable solutions outside of EOF would involve storing the value in contract storage and providing APIs to update the value, but that would incur a cold `SLOAD` gas charge.


The adoption of "Option D - Introspecting EOF" will address all these concerns.

### Limiting available gas in external calls

Limiting gas values runs counter to the "Ban Gas Introspection" theme and would not be supported in EOF.

These variants are trickier. Examples in this doc likely involve values that come from "outside" the VM.

Options not addressed in this document include the EIP-4337 entrypoint contract. But in this case the EIP-4337 team said they would rely on non-EOF entry points to provide this service. AA wallets can be written in EOF code and called by the existing entrypoint code.

The adoption of "Option D - Introspecting EOF" will address all these concerns.

### Introspecting Account Types (Contract or not)

Using `EXTCODESIZE` is a common technique to determine if an address is a contract. Contracts will use it as a sanity check to ensure they are not trying to treat an EOA as a contract and execute its (non-existent) functions. Removing this opcode is a part of the "Ban Code Introspection" theme.

The adoption of "Option D - Introspecting EOF" will address all these concerns.

### Direct use of CREATE/CREATE2

EOF changes the contract creation pathway, as an essential part of the vision in Vitalik's EOF proposal: [Ban Code introspection in EOF accounts](https://ethereum-magicians.org/t/eof-proposal-ban-code-introspection-of-eof-accounts/12113). CREATE/2 depends on code entering contract memory, which makes long term JIT and AOT compilation difficult to impossible depending on if and how the code is altered from its original source.

OpenZeppelin's `Create2` libraries are intended to promote safe usage.  A similiar library can be written for `EOFCREATE` and `TXCREATE`.

Depending on the use cases, the new `EOFCREATE` and `TXCREATE` may be able to handle it if the contracts being deployed are "factory" contracts. Other uses cases such as initializing immutable values would be handled by the new DATA series opcodes and use of the data section. However, dynamically assembling code on the fly would not be possible. Users are encouraged to use libraries like is currently done with `CREATE2` and OpenZeppelin and to roll their own `TXCREATE` solutions with great care.

<!--
### Custom EVM integrations

Projects with customized EVM implementations will need to do work to ensure their customizations are in harmony with the EVM in the new fork. This is the same with changes from `PUSH0` to EIP-7702 to EOF.

The only option that addresses this concern is to not implement EOF. However the changes for Foundry appear to have a very "on-brand" solution.
-->

----


*Special thanks to Kamil from the Solidity team for help in preparing this report*


# Everything about the EVM Object Format (EOF)

The aim of this document is to serve as an explainer and collection of links about EVM Object Format. This is a living document, so updates and changes are expected.

Please leave comments on the [Ethereum Magicians forum](https://ethereum-magicians.org/t/evm-object-format-eof/5727) or the discussions URLs for the specific EIPs.

[toc]

### Motivation

On-chain deployed EVM bytecode contains no pre-defined structure today. Code is typically validated in clients to the extent of `JUMPDEST` analysis at runtime, every single time prior to execution. This poses not only an overhead, but also a challenge for introducing new or deprecating old features.

Validating code during the contract creation process allows code versioning without an additional version field in the account. Versioning is a useful tool for introducing or deprecating features, especially for larger changes (such as significant changes to control flow, or features like account abstraction).

### Two hard forks

In order to have strong guarantees that code looking like EOF1 is actually valid EOF1 code, we must a) introduce deploy time validation of all submitted contracts, and b) ensure that no such (malicious) contract is deployed before this feature is enabled on mainnet.

A solution to this is to first reject a specific starting byte for new contracts, then select the shortest prefix (starting with this byte) which does not exists in any already deployed contract, and finally use this as the prefix of the EOF format. This way it is not possible to encounter existing code "masquerading" as EOF1.

### Timeline

#### London

[EIP-3541](https://eips.ethereum.org/EIPS/eip-3541) is rolled out, which rejects any new contracts starting with the `0xEF` byte. After the forkblock, we'll inspect all existing code on chain to select the prefix.

#### Shanghai

The format described in [EIP-3540](https://eips.ethereum.org/EIPS/eip-3540) introduces a simple and extensible container with a minimal set of changes required to both clients and languages, and introduces the concept of validation.

The first tangible feature it provides is separation of executable code and non-executable data. This separation is especially beneficial for on-chain code validators (like those utilised by layer-2 scaling tools, such as Optimism), because they can distinguish code and data (this includes deployment code and constructor arguments too). Code and data separation can result in ease of use and significant gas savings for such use cases. Additionally, various (static) analysis tools can also benefit, though off-chain tools can already deal with existing code, so the impact is smaller.

Time permitting, we also would like to include [EIP-3670](https://eips.ethereum.org/EIPS/eip-3670) in Shanghai. That EIP extends EOF with deploy time validation of the code section, such that code using unassigned instructions or truncated PUSHes are rejected.

#### Cancun and later

We aim to provide a new extension, EOF2, which overhauls control flow. The currently considered ideas include:
- Including a `JUMPDEST`-table (to avoid analysis at execution time) or removing `JUMPDESTs` entirely.
- Introducing static jumps (with relative addresses) and jump tables, and disallowing dynamic jumps at the same time.
- Representing functions as individual code sections instead of subroutines.

Note that some of these are mutually exclusive, and all of them need to be investigated for both technical complexity, benefits to Ethereum clients, benefits to users.

Potential other features for the future could include:
- Requiring code section(s) to be terminated by STOP. (Assumptions like this can provide significant speed improvements in interpreters, such as a speed up of ~7% seen in evmone.)
- Any feature needing multi-byte opcodes (without any workarounds).
- Introducing a specific section for the [EIP-2938 Account Abstraction](https://eips.ethereum.org/EIPS/eip-2938) “top-level AA execution frame”, simplifying the proposal.

### Core Specifications/Explainers
- [EIP-3540: EVM Object Format (EOF) v1](https://eips.ethereum.org/EIPS/eip-3540)
- [EIP-3541: Reject new contracts starting with the 0xEF byte](https://eips.ethereum.org/EIPS/eip-3541)
- [EIP-3670: EOF - Code Validation](https://eips.ethereum.org/EIPS/eip-3670)
- [EIP-4200: Static relative jumps](https://eips.ethereum.org/EIPS/eip-4200)
- [Magicians forum: EOF](https://ethereum-magicians.org/t/evm-object-format-eof/5727)
- PEEPanEIP Explainer (June 2021): [Video](https://youtu.be/E02THhW-yTE) and [Slides](https://notes.ethereum.org/@ipsilon/eof-peepaneip-presentation)

### Various Potential Use Cases
- [EIP-2938 Account Abstraction with EVM Object Format](https://notes.ethereum.org/@axic/account-abstraction-with-eof)
- [Validation Focused Smart Contract Wallets](https://ethereum-magicians.org/t/validation-focused-smart-contract-wallets/6603)
- [EIP-3690: EOF - JUMPDEST Table](https://eips.ethereum.org/EIPS/eip-3690)
- [EIP-4750: EOF - Functions](https://eips.ethereum.org/EIPS/eip-4750)

### Additional Links
- [Original write up](https://notes.ethereum.org/@axic/evm-object-format) (explains some background on EVM and motivation)
- [Feedback from chriseth](https://notes.ethereum.org/BgA648JCQ6uVHPUVFrLzkQ?view)
- [Some ideas (datacopy, jumpv)](https://notes.ethereum.org/t-1tLFnLSKCtLZpb-Rw0IA?view)
- [Tracking issue for Shanghai](https://github.com/ethereum/pm/issues/329)
- [Magicians forum: Ethereum account versioning](https://ethereum-magicians.org/t/ethereum-account-versioning/3508) has links to all/many previous ideas and discussions

### Implementations
- [geth](https://github.com/ethereum/go-ethereum/pull/22958)
- [evmone](https://github.com/ethereum/evmone/pull/303)
- [Solidity](https://github.com/ethereum/solidity/tree/eof1)
- [Validator in C++](https://github.com/ethereum/evmone/pull/303)
- [Validator in Python](https://gist.github.com/axic/c7a3cbeafad0ca867b04b784c1a757a8)
---
title: Ipsilon website
---

# Ipsilon <small>{[twitter.com/teamipsilon](https://twitter.com/teamipsilon) and *team at ipsilon.xyz*}</small>

![](https://pbs.twimg.com/profile_images/1544472551996497920/ak1xr9iC_200x200.jpg)

> 💡The name is a reference to the state transition function defined in the [Yellow Paper](https://github.com/ethereum/yellowpaper).

[toc]

## Who are Ipsilon?

Ipsilon is a research & development team funded by the [Ethereum Foundation](https://ethereum.foundation).

The team formed in late 2017 under the name Ewasm with the goal to bring WebAssembly to Ethereum. Research included both EVM and WebAssembly starting mid-2019, and as of 2021 it mostly shifted to EVM. To avoid confusion we have rebranded early 2021 to Ipsilon.

## What do we do?

The team’s core concern is the execution environment / engine of Ethereum (aka the EVM or any future versions or replacements of it). We provide analysis and implementation of own and third party proposals (i.e. new EIPs proposing changes to the EVM), provide tooling (evmc, evmone, fizzy), and support existing teams (e.g. Solidity, go-ethereum, Silkworm, Erigon) with implementation and analysis.

We publish our findings on [notes.ethereum.org/@ipsilon](https://notes.ethereum.org/@ipsilon), [Ethereum Magicians](https://ethereum-magicians.org), and [ethresear.ch](https://ethresear.ch). We also give talks at ecosystem events like Protocol Workshops, Devcon, EVM Summit, EthCC, EthOnline, Wasm on Web3, LisCon, etc.

Additionally, a regular summary on blog.ethereum.org: [December 2022](https://blog.ethereum.org/2022/12/29/supported-teams-roundup-22#ipsilon-execution-environment-research), [December 2021](https://blog.ethereum.org/2021/12/22/ef-supported-teams-research-and-development-update-2021-pt-3/#ipsilon), [August 2021](https://blog.ethereum.org/2021/08/12/ef-supported-teams-research-and-development-update-2021-pt-2/#ipsilon-previously-ewasm), [April 2021](https://blog.ethereum.org/2021/04/26/ef-supported-teams-research-and-development-update-2021-pt-1/#ewasm), [December 2020](https://blog.ethereum.org/2020/12/09/ef-supported-teams-research-and-development-update-2020-pt-2/#ewasm), [April 2020](https://blog.ethereum.org/2020/04/14/ef-supported-teams-research-and-development-update-2020-pt-1/#ewasm), [December 2019](https://blog.ethereum.org/2019/12/03/ef-supported-teams-research-and-development-update-2019-pt-2/#ewasm), [June 2019](https://blog.ethereum.org/2019/06/21/ef-supported-teams-development-report-2019-pt-1/#ewasm).

We also organize the [EVM Summit](https://evmsummit.org/), a conference dedicated to discussions around the EVM. The inaugural summit was held during Devonnect Istanbul in November 2023, followed by an *EVM mini-Summit* at Devcon Bangkok in November 2024.

## Current Projects

### EOF

EVM Object Format (EOF) is the next step in the evolution of the EVM. It introduces a long sought format for separation of code and data, allows versioning, and enables a much easier and faster way to improve the EVM.

The work can be most accurately tracked on [evmobjectoformat.org](https://evmobjectformat.org) and the [ipsilon/eof](https://github.com/ipsilon/eof) repository. It is currently slated for adoption in the [Osaka network update](https://eips.ethereum.org/EIPS/eip-7607).

<small>For the earlier versions (Shanghai/Cancun/Prague), work was tracked in [Everything About EOF](https://notes.ethereum.org/@ipsilon/evm-object-format-overview) and [EOFv1 Checklist](https://notes.ethereum.org/@ipsilon/eof1-checklist).</small>

### EVMMAX

EVMMAX aka **EVM** **M**odular **A**rithmetic e**X**tensions is the spiritual successor of evm384 (listed below). This extension of the EVM introduces a few instructions, which allow Montgomery multiplication to be done in an efficient manner.

### evmone + evmc

[evmone](https://github.com/ethereum/evmone) is a very fast C++ implementation of the Ethereum Virtual Machine (EVM). It can be integrated via the [EVMC API](https://github.com/ethereum/evmc) or via a native API. It is used by [Solidity](https://soliditylang.org) for testing and in the [Silkworm](https://github.com/torquem-ch/silkworm) Ethereum client.

### EIPs

The team is currently pursuing a number of EIPs: [EIP-1985](https://eips.ethereum.org/EIPS/eip-1985) (Sane limits for EVM parameters), [EIP-3540](https://eips.ethereum.org/EIPS/eip-3540) (EVM Object Format), [EIP-3670](https://eips.ethereum.org/EIPS/eip-3670) (Code Validation), [EIP-3690](https://eips.ethereum.org/EIPS/eip-3690) (JUMPDEST table), [EIP-4200](https://eips.ethereum.org/EIPS/eip-4200) (Static relative jumps), and [EIP-4750](https://eips.ethereum.org/EIPS/eip-4750) (Functions)

In has also contributed to numerous adopted EIPs: [EIP-140](https://eips.ethereum.org/EIPS/eip-140) (`REVERT` instruction), [EIP-141](https://eips.ethereum.org/EIPS/eip-145) (`INVALID` instruction), [EIP-145](https://eips.ethereum.org/EIPS/eip-145) (Bitwise shifting), [EIP-2681](https://eips.ethereum.org/EIPS/eip-2681) (Limit account nonce), and [EIP-3541](https://eips.ethereum.org/EIPS/eip-3541) (Reject code starting with 0xEF), [EIP-3855](https://eips.ethereum.org/EIPS/eip-3855) (`PUSH0` instruction), [EIP-3860](https://eips.ethereum.org/EIPS/eip-3860) (Limit and meter initcode) and [EIP-5656](https://eips.ethereum.org/EIPS/eip-5656) (`MCOPY` instruction).

And some proposals which are postponed or superseded: [EIP-663](https://eips.ethereum.org/EIPS/eip-663) (Unlimited SWAP and DUP instructions), [EIP-2045](https://eips.ethereum.org/EIPS/eip-2045) (Particle Gas Costs), and [EIP-2926](https://eips.ethereum.org/EIPS/eip-2926) (Code Merkleization).

Scroll down for a complete list.

### EVM Summit

The EVM Summit is a recurring event bringing together various groups interested in evolving the EVM. For further information visit the [website](https://evmsummit.org/) or [twitter](https://twitter.com/evmsummit).

### EVM support

We support other projects and teams with review and feedback on EVM changes. Currently we work with the Verkle team with regards to changes to the EVM and code chunking.

## Past Projects

### fizzy <small>(on hold)</small>

[fizzy](https://github.com/wasmx/fizzy) is a fast, deterministic, and pedantic WebAssembly interpreter written in C++.

See [this talk](https://www.youtube.com/watch?v=7C0YuLSHmhI) about Fizzy and [this talk](https://www.youtube.com/watch?v=St1PxQB3uPk) about WebAssembly peculiarities at the *Wasm on Web3 2021* conference.

### ASE <small>(on hold)</small>

Address Space Extension (ASE) is a working group exploring the ways to allow Ethereum to use 32-byte long addresses as opposed to the current 20-byte long ones. This would be beneficial to allow better quantum-resistant options and makes the state expiry proposal *easier* to accomplish. However it poses a large and mostly backwards-incompatble change.

The [`address-space` tag](https://ethereum-magicians.org/tag/address-space) on Ethereum Magicians includes most of the relevant links.

### Ewasm <small>(deprecated)</small>

Ewasm was a research project adapting WebAssembly for blockchain use cases (which means ensuring determinism, including metering), and a lot of engineering implementing it in Ethereum clients and providing a vast array of tooling. A good starting point is the [design](https://github.com/ewasm/design) and the [benchmarking](https://github.com/ewasm/benchmarking) repository. While Ethereum Mainnet has not yet adopted WebAssembly, a lot of other projects have based their work on or took inspiration from Ewasm.

### evm384 <small>(superseded)</small>

This was a proposal to introduce primitives into the EVM for efficient 384-bit arithmetics. It was later extended to higher and variable widths. It would allow efficient implementation of various elliptic curves (e.g. BLS12-381) and hashing functions (e.g. MiMC) without precompiles. The progress and links to the various stages of work can be [followed in this Ethereum Magicians thread](https://ethereum-magicians.org/t/evm384-feedback-and-discussion/4533).

### Eth 2.0 Phase 2 <small>(superseded)</small>

In an earlier design of "Eth 2.0", during Phase 2 execution was to be enabled on shards. The team has researched various strategies to include execution on the Beacon chain and/or on the shards (e.g. Scout and numerous Execution Environments), to interface with the Eth1 shard, and to support cross-shard communication (e.g. Eth1x64).

Reading the [December 2020](https://blog.ethereum.org/2020/12/09/ef-supported-teams-research-and-development-update-2020-pt-2/#ewasm), [April 2020](https://blog.ethereum.org/2020/04/14/ef-supported-teams-research-and-development-update-2020-pt-1/#ewasm), [December 2019](https://blog.ethereum.org/2019/12/03/ef-supported-teams-research-and-development-update-2019-pt-2/#ewasm), and [June 2019](https://blog.ethereum.org/2019/06/21/ef-supported-teams-development-report-2019-pt-1/#ewasm) updates paint a good overview of the work performed.

### Stateless Ethereum <small>(on hold)</small>

Under the Stateless Ethereum umbrella we have worked on code merkleization, witness encoding, and light-client syncing.

The [December 2020](https://blog.ethereum.org/2020/12/09/ef-supported-teams-research-and-development-update-2020-pt-2/#ewasm) update gives a good overview.

## Team members

### Active members

| Name                        | Github                       | Twitter                     |
| --------------------------- | ---------------------------- | ---------------------------- |
| Alex Beregszaszi            | https://github.com/axic      | https://twitter.com/alexberegszaszi |
| Andrei Maiboroda            | https://github.com/gumb0     | https://twitter.com/gumb00 |
| Danno Ferrin                | https://github.com/shemnon   | https://twitter.com/shemnon |
| Jose Hugo de la cruz Romero | https://github.com/hugo-dc   | https://twitter.com/hugo_dc |
| Paweł Bylica                | https://github.com/chfast    | https://twitter.com/chfast |
| Piotr Dobaczewski | https://github.com/pdobacz |
| Radosław Zagórowicz         | https://github.com/rodiazet  | https://twitter.com/rodiazet |
| Eniko (EVM Summit) | https://github.com/Eni-G | https://twitter.com/Eniko_G |

### Alumni

<details>
  <summary>Expand</summary>

| Name                        | Github                       |
| --------------------------- | ---------------------------- |
| Casey Detrio                | https://github.com/cdetrio   |
| Everett Hildenbrandt        | https://github.com/ehildenb  |
| Guillaume Ballet            | https://github.com/gballet   |
| Jake Lang                   | https://github.com/jakelang  |
| Jared Wasinger              | https://github.com/jwasinger |
| Lane Rettig                 | https://github.com/lrettig   |
| Martin Becze                | https://github.com/wanderer  |
| Paul Dworzanski             | https://github.com/poemm     |
| Sina Mahmoodi               | https://github.com/s1na      |
| ZX                          | https://github.com/zixuanzh  |

</details>

## Team repositories 
 
 <details>
  <summary>Expand</summary>

| Project     | Repo                     |
| ----------- | ------------------------ |
| Ipsilon org | https://github.com/ipsilon |
| Ewasm org   | https://github.com/ewasm |
| Wasmx org   | https://github.com/wasmx |
| evmc        | https://github.com/ethereum/evmc |
| evmone      | https://github.com/ethereum/evmone |
| intx        | https://github.com/chfast/intx |
| ethash      | https://github.com/chfast/ethash |
| cable       | https://github.com/ethereum/cable |
| cpp-build-env | https://github.com/ethereum/cpp-build-env |
</details>


## Publications

### Articles

- [EVMMAX Breakout - Devcon SEA L1 R&D Workshop](https://notes.ethereum.org/@ipsilon/evmmax-breakout-2024), 2024-11-11 
- [Measurable benefits of EOF](https://notes.ethereum.org/@ipsilon/solidity_eof_poc), 2024-07-29
- [EOFv0 for packaging legacy code in Verkle Trees](https://github.com/ipsilon/eof/blob/main/spec/eofv0_verkle.md), 2024-04-25
- [EOF header access in Verkle](https://notes.ethereum.org/@ipsilon/eof_header_access_in_verkle), 2024-04-24
- [Pairing: Linear function evaluation + point addition/doubling in Jacobian coordinates](https://notes.ethereum.org/@ipsilon/pairing_linear_function_evaluation), 2024-04-12
- [EVMMAX in evmone](https://notes.ethereum.org/@ipsilon/evmmax_in_evmone), 2024-04-05
- [EVMMAX Performance Analysis 1](https://notes.ethereum.org/@ipsilon/evmmax-performance-report-1), 2024-04-04
- [EVMMAX ❤️ ECRecover](https://devfolio.co/projects/evmmax-ecrecovery-bd49), 2023-06-11
- [EVMMAX Edelweiss Breakout](https://notes.ethereum.org/@ipsilon/rJGwVubhs), 2023-01-27
- [Withdrawals – what it could have been?](https://notes.ethereum.org/@axic/BJEbqX13j), 2023-01-26
- [EIP-4844 point evaluation precompile](https://notes.ethereum.org/@axic/HJ6k_Nlis), 2023-01-17
- [Everything about the EVM Object Format (EOF)](https://notes.ethereum.org/@ipsilon/evm-object-format-overview), 2022-04-21
- [Faster Ethash check against difficulty](https://ethresear.ch/t/faster-ethash-check-against-difficulty/11442), 2021-12-06
- [EVM Performance Report — Geth & Go Compiler](https://notes.ethereum.org/@ipsilon/evm-performance-report-geth-and-go-compiler), 2021-11
- [EVM Performance Report — Geth vs evmone](https://notes.ethereum.org/@ipsilon/evm-performance-report-geth-vs-evmone), 2021-11
- [EOF Prefix Selection](https://notes.ethereum.org/@ipsilon/eof-prefix-selection), 2021-10
- [Code Merkleization Practical Implementation & Analysis](https://notes.ethereum.org/@ipsilon/code-merkleization-implementation-analysis), 2021-08
- [Memory Copying in Contracts Deployed on Ethereum](https://notes.ethereum.org/@ipsilon/evm-mcopy-analysis), 2021-06
- [Everything about the EVM Object Format (EOF)](https://notes.ethereum.org/@ipsilon/evm-object-format-overview), 2021 (live document)
- [Code-chunk-cost Analysis](https://notes.ethereum.org/@ipsilon/code-chunk-cost-analysis), 2021-05
- [ASE test cases](https://notes.ethereum.org/@ipsilon/address-space-extension-test-cases), 2021-05
- [Issues with ASE (with a translation map)](https://notes.ethereum.org/@ipsilon/address-space-extension-issues), 2021-05
- [ASE (Address Space Extension) with Translation Map](https://notes.ethereum.org/@ipsilon/address-space-extension-exploration), 2021-05
- [State of block header sync in light clients](https://ethresear.ch/t/state-of-block-header-sync-in-light-clients/8047), 2020-09-29
- [Eth1x64 Variant 1 "Apostille"](https://ethresear.ch/t/eth1x64-variant-1-apostille/7365), 2020-05-07
- [EIP-2315 "Simple Subroutines for the EVM"](https://ethereum-magicians.org/t/eip-2315-simple-subroutines-for-the-evm-analysis/4229) - Analysis, 2020-04-29
- [The curious case of BLOCKHASH and Stateless Ethereum](https://ethresear.ch/t/the-curious-case-of-blockhash-and-stateless-ethereum/7304), 2020-04-20
- [Stateless Mining Strategies](https://ethresear.ch/t/stateless-mining-strategies/7172), 2020-03-23
- [Survey of proposals to reduce block witness size](https://ethresear.ch/t/survey-of-proposals-to-reduce-block-witness-size/7173), 2020-03-23
- [Zero Knowledge Tooling for an Eth 2.0 Wasm Execution Layer](https://ethresear.ch/t/zero-knowledge-tooling-for-an-eth-2-0-wasm-execution-layer/7080), 2020-03-05
- [The Eth1x64 experiment](https://ethresear.ch/t/the-eth1x64-experiment/7195), 2020-03-05
- [zk-MerkleWitnessAndSigRollup, a generic SNARK circuit for stateless contracts](https://ethresear.ch/t/zk-merklewitnessandsigrollup-a-generic-snark-circuit-for-stateless-contracts/7011), 2020-02-23
- [A protocol for cross-shard ETH transfers: even more simpler and transparent](https://ethresear.ch/t/a-protocol-for-cross-shard-eth-transfers-even-more-simpler-and-transparent/6996), 2020-02-20
- [Phase 2 execution prototyping engine (Ewasm Scout)](https://ethresear.ch/t/phase-2-execution-prototyping-engine-ewasm-scout/5509), 2019-05-19
- [Phase One and Done: eth2 as a data availability engine](https://ethresear.ch/t/phase-one-and-done-eth2-as-a-data-availability-engine/5269), 2019-04-07
- [Using VRFs to randomly assign stakers to shards](https://ethresear.ch/t/using-vrfs-to-randomly-assign-stakers-to-shards/4451), 2018-12-03
- [Limiting Last-Revealer Attacks in Beacon Chain Randomness](https://ethresear.ch/t/limiting-last-revealer-attacks-in-beacon-chain-randomness/3705), 2018-10-05
- [Synchronous cross-shard transactions with consolidated concurrency control and consensus (or how I rediscovered Chain Fibers)](https://ethresear.ch/t/synchronous-cross-shard-transactions-with-consolidated-concurrency-control-and-consensus-or-how-i-rediscovered-chain-fibers/2318), 2018-06-21

### EIPs

*This includes Ipsilon-driven proposals, and also collaborations by Ipsilon members.*

- 🚧 [EIP-7830: Contract size limit increase for EOF](https://github.com/ethereum/EIPs/pull/9074), 2024-11-29
- 🚧 [EIP-7823: Set upper bounds for MODEXP](https://eips.ethereum.org/EIPS/eip-7823), 2024-11-11
- 🚧 [EIP-7822: EVM Modular Arithmetic Extensions](https://github.com/ethereum/EIPs/pull/9041), 2024-10-07
- 🚧 [EIP-7761: HASCODE instruction](https://eips.ethereum.org/EIPS/eip-7761), 2024-09-01
- 🚧 [EIP-7747: EVM Modular Arithmetic Extensions](https://github.com/ethereum/EIPs/pull/8743), 2024-07-20
- 🚧 [EIP-7676: EOF - Prepare for Address Space Extension](https://eips.ethereum.org/EIPS/eip-7676), 2024-04-03
- 🚧 [EIP-7698: EOF - Creation transactions](https://eips.ethereum.org/EIPS/eip-7698), 2024-04-24
- 📖 [EIP-7620: EOF - Contract Creation](https://eips.ethereum.org/EIPS/eip-7620), 2024-02-12
- 📖 [EIP-7480: EOF - Data section access instructions](https://eips.ethereum.org/EIPS/eip-7480), 2023-08-11
- 📖 [EIP-7069: Revamped CALL instructions](https://eips.ethereum.org/EIPS/eip-7069), 2023-05-05
- 🚧 [EIP-6690: EVM Modular Arithmetic Extensions (EVMMAX) - Decoupled from EOF](https://eips.ethereum.org/EIPS/eip-6690), 2023-03-15
- 🚧 [EIP-6601: EVM Modular Arithmetic Extensions](https://github.com/ethereum/EIPs/pull/6601), 2023-03-02
- ♻️ [EIP-6046: Replace SELFDESTRUCT with DEACTIVATE](https://eips.ethereum.org/EIPS/eip-6046), 2022-11-25 (another option was chosen by ACDE)
- 🚧 [EIP-5843: EVM Modular Arithmetic Extensions](https://github.com/ethereum/EIPs/pull/5843), 2022-10-26
- ✅ [EIP-5656: MCOPY instruction](https://eips.ethereum.org/EIPS/eip-5656), 2021-02-01
- 📖 [EIP-5450: EOF - Stack Validation](https://eips.ethereum.org/EIPS/eip-5450), 2022-08-12
- 🚧 [EIP-5000: MULDIV instruction](https://eips.ethereum.org/EIPS/eip-5000), 2022-03-14
- 🚧 [EIP-4803: Limit transaction gas to a maximum of 2^63-1](https://eips.ethereum.org/EIPS/eip-4803), 2022-02-02
- 📖 [EIP-4750: EOF - Functions](https://eips.ethereum.org/EIPS/eip-4750), 2022-01-10
- 📖 [EIP-4200: Static relative jumps](https://eips.ethereum.org/EIPS/eip-4200), 2021-07-16
- ✅ [EIP-3860: Limit and meter initcode](https://eips.ethereum.org/EIPS/eip-3860), 2021-07-16
- ✅ [EIP-3855: PUSH0 instruction](https://eips.ethereum.org/EIPS/eip-3855), 2021-02-19
- ❔ [EIP-3690: EOF - JUMPDEST Table](https://eips.ethereum.org/EIPS/eip-3690), 2021-06-23
- 📖 [EIP-3670: EOF - Code Validation](https://eips.ethereum.org/EIPS/eip-3670), 2021-06-23
- ✅ [EIP-3541: Reject new contract code starting with the 0xEF byte](https://eips.ethereum.org/EIPS/eip-3541), 2021-03-16
- 📖 [EIP-3540: EVM Object Format (EOF) v1](https://eips.ethereum.org/EIPS/eip-3540), 2021-03-16
- ♻️ [EIP-2926: Chunk-Based Code Merkleization](https://eips.ethereum.org/EIPS/eip-2926), 2020-08-25 (replaced by Verkle trees)
- ✅ [EIP-2681: Limit account nonce to 2^64-1](https://eips.ethereum.org/EIPS/eip-2681), 2020-04-25
- ♻️ [EIP-2677: Limit size of initcode](https://eips.ethereum.org/EIPS/eip-2677), 2020-05-18 (replaced by [EIP-3860](https://eips.ethereum.org/EIPS/eip-3860)) 
- ❔ [EIP-2488: Deprecate the CALLCODE opcode](https://eips.ethereum.org/EIPS/eip-2488), 2019-12-20
- ♻️ [EIP-2046: Reduced gas cost for static calls made to precompiles](https://eips.ethereum.org/EIPS/eip-2046), 2019-05-17 (replaced by [EIP-2929](https://eips.ethereum.org/EIPS/eip-2929))
- ❔ [EIP-2045: Particle gas costs for EVM opcodes](https://eips.ethereum.org/EIPS/eip-2045), 2019-05-17
- ❔ [EIP-2014: Extended State Oracle](https://eips.ethereum.org/EIPS/eip-2014), 2019-05-10
- ❔ [EIP-2003: EVMC modules for implementations of precompiled contracts](https://eips.ethereum.org/EIPS/eip-2003), 2019-05-09
- 🚧 [EIP-1985: Sane limits for certain EVM parameters](https://eips.ethereum.org/EIPS/eip-1985), 2018-08-01
- 🚧 [EIP-1803: Rename opcodes for clarity](https://eips.ethereum.org/EIPS/eip-1803), 2017-07-28
- ❔ [EIP-1571: EthereumStratum/2.0.0](https://eips.ethereum.org/EIPS/eip-1571), 2018-11-09
- ♻️ [EIP-1380: Reduced gas cost for call to self](https://eips.ethereum.org/EIPS/eip-1380), 2018-08-31 (replaced by [EIP-2929](https://eips.ethereum.org/EIPS/eip-2929))
- ♻️ [EIP-1355: Ethash 1a](https://eips.ethereum.org/EIPS/eip-1355), 2018-08-26 (deprecated by "The Merge")
- ❔ [EIP-1352: Specify restricted address range for precompiles/system contracts](https://eips.ethereum.org/EIPS/eip-1352), 2018-07-27
- ✅ [EIP-1052: EXTCODEHASH opcode](https://eips.ethereum.org/EIPS/eip-1052), 2018-05-02
- 📖 [EIP-663: Unlimited SWAP and DUP instructions](https://eips.ethereum.org/EIPS/eip-663), 2017-07-03
- ♻️ [EIP-615: Subroutines and Static Jumps for the EVM](https://eips.ethereum.org/EIPS/eip-615), 2016-12-10 (replaced by EOF)
- ✅ [EIP-145: Bitwise shifting instructions in EVM](https://eips.ethereum.org/EIPS/eip-145), 2017-02-13
- ✅ [EIP-141: Designated invalid EVM instruction](https://eips.ethereum.org/EIPS/eip-141), 2017-02-09
- ✅ [EIP-140: REVERT instruction](https://eips.ethereum.org/EIPS/eip-140), 2017-02-06

Legend:
- ✅ Adopted
- 📖 Under review
- 🚧 Currently being worked on
- ❔ Not in focus currently
- ♻️ Replaced
- ❌ Withdrawn

### Talks


- [Changes to the L1 EVM versus L2s](https://app.devcon.org/schedule/MFYXWT) ([YT](https://www.youtube.com/watch?v=tc2UIcqMU8E)), EVM mini-Summit - November 2024
- [The Future of EOF: Layer 1, Layer 2, and Beyond!](https://app.devcon.org/schedule/9EBQ3H) ([YT](https://www.youtube.com/watch?v=NeKMerFPJoM)), EVM mini-Summit - November 2024
- [EVMMAX. Fast Modular Arithmetic in EVM](https://app.devcon.org/schedule/7CWEHH) ([YT]()), EVM mini-Summit - November 2024
- [Zoom in on EOF stack validation](https://app.devcon.org/schedule/YYGYGF), EVM mini-Summit - November 2024
- [EVMMAX: advanced Elliptic Curve Cryptography in EVM ](https://app.streameth.org/devconnect/evm_summit/session/evmmax__advanced_elliptic_curve_cryptography_in_evm), EVMSummit 0 - November 2023
- [EVM Future Panel](https://app.streameth.org/devconnect/evm_summit/session/evm_future_panel_discussion), EVMSummit 0 - November 2023
- [EVM Governance Panel](https://app.streameth.org/devconnect/evm_summit/session/evm_governance_panel_discussion), EVMSummit 0 - November 2023
- [PEEPanEIP #118: EIP-5656: MCOPY - Memory copying instruction](https://www.youtube.com/watch?v=m_YzKuL2VxM),  PEEPanEIP - September 2023
- [EVMMAX](https://www.youtube.com/watch?v=mA-UEo4547k), zkDays at EthWarsaw - September 2023
- [EVM-first EIPs Discussion](https://www.youtube.com/watch?v=6ZMd4xmX8D4), Devcon6 - October 2022
- [What's next in EVM](https://www.youtube.com/watch?v=pyxtgilJgKg), Devcon6 - October 2022
- Deep dive into EOF, Protocol R&D Workshop - October 2022
- [PEEPanEIP#70: EVM Object Format (EIP-3540 & EIP-3670)](https://www.youtube.com/watch?v=GMeRA-xPp-E) ([slides](https://notes.ethereum.org/@ipsilon/peepaneip-eof1)), PEEPanEIP - May 2022
- [History and Future of EVM Jumps](https://www.youtube.com/watch?v=8Cp8IsmIJl4) ([slides](https://docs.google.com/presentation/d/e/2PACX-1vQUeg3dB21PMNQ8Bnx8ClUpc_Jyt1INypJTzUcD4RrtZyCOETOS3lMSTs29cZhM4_-JwlbCQDoryHc5/pub)), Secureum TrustX Amsterdam - April 2022
- [What's next in EVM?](https://www.youtube.com/watch?v=w5QxYFCXm7A) ([slides](https://axic.github.io/notes/trustx_eof/)), Secureum TrustX Amsterdam - April 2022
- EVM Object Format ([slides](https://axic.github.io/notes/devconnect_eof/)), Protocol R&D Amsterdam - April 2022
- [PEEPanEIP#67: EIP-3860: Limit and meter initcode](https://www.youtube.com/watch?v=PJQdhPR5BJ0) ([slides](https://notes.ethereum.org/@ipsilon/peepaneip-3860)),  PEEPanEIP - April 2022
- [PEEPanEIP#66: EIP-3855: PUSH0 instruction](https://www.youtube.com/watch?v=AIuJg6pkJxs) ([slides](https://notes.ethereum.org/@ipsilon/peepaneip-3855)),  PEEPanEIP - April 2022
- [The future of EVM - EVM Object Format](https://youtu.be/wa1qCKR8ig8?t=5264), Ethereum Meetup Berlin - November 2021
- The future of EVM - EVM Object Format, Liscon - October 2021
- [Weird quirks while testing WebAssembly](https://www.youtube.com/watch?v=St1PxQB3uPk), Wasm on Web3 - October 2021
- [Fizzy — A deterministic interpreter](https://www.youtube.com/watch?v=7C0YuLSHmhI), Wasm on Web3 - October 2021
- [PEEPanEIP#36: EIP-3540 & EIP-3541](https://www.youtube.com/watch?v=E02THhW-yTE) ([slides](https://notes.ethereum.org/@ipsilon/eof-peepaneip-presentation)), PEEPanEIP - June 2021
- [EVM384 - Doing Fast Crypto in EVM](https://www.youtube.com/watch?v=vlrLl1r66GU), ETHOnline - October 2020
- [State of Light Client Sync](https://www.youtube.com/watch?v=OXMINRtWWhI), ETHOnline - October 2020
- [Scaling ethereum 1.x with WASM precompiles](https://www.youtube.com/watch?v=nDHM6Ukign8), EthCC - March 2020
- [EVM Bytecode Merklization](https://www.youtube.com/watch?v=d4CqECZjmKo), EthCC - March 2020
- [Optimization techniques for EVM implementations](https://www.youtube.com/watch?v=2ZAh7NmjOXM), Devcon5 - October 2019
- [Ewasm 2.0: State Execution in Eth 2.0](https://www.youtube.com/watch?v=LXMGyHFmTr8), Devcon5 - October 2019
- [Ewasm: Past, Present, Future](https://www.youtube.com/watch?v=aoaJIaq_fF8), Devcon5 - October 2019
- [EVM Roundtable: Everything You Wanted to Ask, But Were Afraid To](https://www.youtube.com/watch?v=hhDjviZegbA), Devcon5 - October 2019
- [Ewasm overview and the precompile problem - Part 2](https://www.youtube.com/watch?v=a9hbycBMr_A), Wasm Workshop Berlin - June 2019
- [Ewasm overview and the precompile problem - Part 1](https://www.youtube.com/watch?v=YW6hszjjMqo), Wasm Workshop Berlin - June 2019
- [Wasm for blockchain & Eth2 execution](https://www.youtube.com/watch?v=XxMTGi4gDfE), Wasm Workshop Berlin - June 2019
- [Eth 2 Phase 2 Execution Engine](https://www.youtube.com/watch?v=RW7K3JQOZOg&t=6240s), Scaling Ethereum - June 2019
- [Ewasm Updates](https://www.youtube.com/watch?v=HaT-BIzWSew?t=20m30s), Eth1.x/Istanbul Planning Workshop - April 2019
- [Gas Token: Killing the Final Boss](https://www.youtube.com/watch?v=HaT-BIzWSew&t=2h14m00s), Eth1.x/Istanbul Planning Workshop - April 2019
- [Ewasm Ethereum 1.x update](https://www.youtube.com/watch?v=BEgXRjr5Yao), EthCC - March 2019
- [Turbo eWASM](https://www.youtube.com/watch?v=V1mDCx_gpZk), Berlin Ethereum Meetup - December 2018
- [Ewasm: Ethereum-flavored WebAssembly and Ethereum 2.0 - Part 2](https://www.youtube.com/watch?v=W4T4Q_SUQt0), Devcon4 - October 2018
- [Ewasm: Ethereum-flavored WebAssembly and Ethereum 2.0 - Part 1](https://www.youtube.com/watch?v=LXMGyHFmTr8), Devcon4 - October 2018
- [WebAssembly for Web3.0](https://www.youtube.com/watch?v=H-Wz4lL3LMc), Web3 Summit - October 2018
- [Deterministic Wasm for Web3](https://www.youtube.com/watch?v=HwXihMcwUh4), Web3 Summit - October 2018
- [Ethereum WebAssembly: the Future of Ethereum Smart Contracts](https://www.youtube.com/watch?v=bXzlz665YUw), ETHIS - September 2018
- [Ewasm Session](https://www.youtube.com/watch?v=2eISBAbT3GM), ECDC - June 2018
- [Ewasm Workshop](https://www.youtube.com/watch?v=qDzrbj7dtyU), ETH BA - May 2018
- [Releasing the Hera with EVM C](https://www.youtube.com/watch?v=mT3hfK7053k), EthCC - March 2018
- [Ewasm](https://www.youtube.com/watch?v=tbDCkPP_kPw), EDCON - March 2017
- [Panel: Evolving the EVM](https://www.youtube.com/watch?v=qO9BTA7vVeQ), Devcon2 - September 2016
- Ewasm, Devcon2 - September 2016
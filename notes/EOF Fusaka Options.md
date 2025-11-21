# EOF Fusaka Options

The EVM Object Format (EOF) has ignited ongoing debate within the Ethereum community, aiming to modernize the Ethereum Virtual Machine’s (EVM) structure, add long-requested features, and enable future protocol upgrades. Initially targeting the "Shanghai" fork and refined through Cancun’s testing and Prague’s planning, EOF has garnered significant input from core developers, tool builders, and language teams (e.g., Solidity, Vyper). Yet, as its scope has expanded—from foundational changes like code/data separation to bold proposals like introspection bans—consensus on its final form remains elusive.

This document, crafted as a "decision tree for EOF/EVM changes in Fusaka" at the request of Tim Beiko, facilitator of the All-Core-Devs Execution call (ACDE), seeks to reconcile these divergent views by presenting four clear options for how to proceed with EOF. This clarity is vital: feedback often arrives late and conflicted, surfacing after designs are built and tested on devnets or testnets, forcing stakeholders to balance innovation, complexity, and stability post-investment. The recent EOF devnet (eof-devnet-0) and ongoing testnet plans highlight this tension, showcasing EOF’s potential alongside its implementation challenges.

To address this, we propose four distinct EOF options for Fusaka:

* (A) - **Complete EOF**: The current "Osaka" plan, a comprehensive overhaul with all proposed features. <br/> Historically referred to as "Mega EOF"
* (B) - **Minimal EOF**: A leaner version, rooted in the Shanghai-era proposal with minimal alterations.<br/>Historically referred to as "Big EOF"
* \(C\) - **Baseline EOF**: A middle ground, balancing features and feasibility while avoiding controversial bans.<br/>This option is new for this document.
* (D) - **Introspecting EOF**: The current "Osaka" plan, removing the Gas Introspection theme and not banning the `EXTCODE*` Opcodes.<br/>This option is new for this document.

While this document centers on these EOF options, some advocates push alternatives like piecemeal adoption and structural ossification. Piecemeal solutions—introducing EOF features incrementally via separate EIPs across multiple forks—might seem to ease immediate complexity but ultimately yield a more convoluted, resource-intensive EVM once complete, as the lack of a unified structure magnifies integration woes. Worse, their staggered rollouts amplify scheduling risks: EVM changes are consistently deprioritized in fork planning, often delayed by other upgrades, rendering deployment of all pieces in a timely fashion unlikely. This fragmentation risks stranding efforts midstream, making structural ossification—where significant EVM changes cease—a more probable outcome, as momentum falters and resources shift elsewhere.

## Overview

Each option is assessed across its EIPs, client development impacts, developer tooling effects, and testing requirements, culminating in a summary chart. This framework equips ACDE participants with the data to choose between pursuing EOF’s ambitious scope or scaling back to a practical subset, guided by community priorities like performance gains, long-term flexibility, or immediate stability. Prepared as of March 24, 2025, this document invites collective input to shape a unified path forward.

**Table of Contents**

* [EIPs](#EIPs)
* [Client Development Impacts](#Development)
* [Developer Tools Impacts](#Developer-Tooling)
* [Testing Impacts](#Testing)
* [Summary Chart](#Comparison-Chart)

## EIPs

### (A) - Complete EOF

The first option represents the status quo of the current plan - proceed with the version of EOF that is in the current "Osaka" meta spec, also known as Complete EOF.

Complete EOF is the result of multiple iterations of the design process that included all feedback such as Vitalik's proposal to [ban code introspection](https://ethereum-magicians.org/t/eof-proposal-ban-code-introspection-of-eof-accounts/12113), Vyper's request for an `EXCHANGE` operation, and Solidity's request to enable tail calls with `JUMPF`.

**eof-devnet-0**

* [EIP-3540](https://eips.ethereum.org/EIPS/eip-3540): EOF - EVM Object Format v1
* [EIP-3670](https://eips.ethereum.org/EIPS/eip-3670): EOF - Code Validation
* [EIP-4200](https://eips.ethereum.org/EIPS/eip-4200): EOF - Static relative jumps
* [EIP-4750](https://eips.ethereum.org/EIPS/eip-4750): EOF - Functions
* [EIP-5450](https://eips.ethereum.org/EIPS/eip-5450): EOF - Stack Validation
* [EIP-6206](https://eips.ethereum.org/EIPS/eip-6206): EOF - JUMPF and non-returning functions
* [EIP-7480](https://eips.ethereum.org/EIPS/eip-7480): EOF - Data section access instructions
* [EIP-663](https://eips.ethereum.org/EIPS/eip-663): SWAPN, DUPN and EXCHANGE instructions
* [EIP-7069](https://eips.ethereum.org/EIPS/eip-7069): Revamped CALL instructions
* [EIP-7620](https://eips.ethereum.org/EIPS/eip-7620): EOF Contract Creation
* [EIP-7698](https://eips.ethereum.org/EIPS/eip-7698): EOF - Creation transaction

**eof-devnet-1**

* [EIP-7873](https://eips.ethereum.org/EIPS/eip-7873): EOF - TXCREATE and InitcodeTransaction type
* Remove EIP-7698

**eof-devnet-2**

* [EIP-7834](https://eips.ethereum.org/EIPS/eip-7834): Separate Metadata Section for EOF
* [EIP-7761](https://eips.ethereum.org/EIPS/eip-7761): EXTCODETYPE instruction
* [EIP-7880](https://eips.ethereum.org/EIPS/eip-7780): EOF - EXTCODEADDRESS instruction
* [EIP-5920](https://eips.ethereum.org/EIPS/eip-5920): PAY opcode

The major difference between this and all the other proposals is that Complete EOF retains the themes of Ban Code Introspection (implemented by EIP-7620, EIP-7698, and 7873), and Ban Gas Introspection (principally implemented by EIP-7069). Several EIPs are added to address community requested features that address gaps created by the bans (EIP-7761, EIP-7880, EIP-5920, EIP-7834).

### (B) - Minimal EOF

Minimal EOF represents the output of the EOF Breakout Room when [@lightclients](https://x.com/lightclients) was the facilitator.

As [tweeted by @lightclients](https://x.com/lightclients/status/1593270268956270594) the list of EIPs is

* [EIP-3540](https://eips.ethereum.org/EIPS/eip-3540): EOF - EVM Object Format v1
* [EIP-3670](https://eips.ethereum.org/EIPS/eip-3670): EOF - Code Validation
* [EIP-4200](https://eips.ethereum.org/EIPS/eip-4200): EOF - Static relative jumps
* [EIP-4750](https://eips.ethereum.org/EIPS/eip-4750): EOF - Functions
* [EIP-5450](https://eips.ethereum.org/EIPS/eip-5450): EOF - Stack Validation

To address an initcode issue, the following is also required:

* [EIP-7698](https://eips.ethereum.org/EIPS/eip-7698): EOF - Creation transaction

Notably absent are stack opcodes and introspection bans with their related EIPs. EIP-7698 is included to resolve a Shanghai-era limitation where developers could not append extra data to containers in deployment scripts, a factor in its initial deferral.

Banned opcodes are limited to `JUMP`, `JUMPI`, `PC`, `SELFDESTRUCT`, and `CALLCODE`. Dynamic `JUMP` and `JUMPI` are removed for stack validation, rendering `PC` unnecessary, while `SELFDESTRUCT` and `CALLCODE` are long-deprecated opcodes excluded from EOF containers.

### \(C\) - Baseline EOF

Baseline EOF offers a middle-ground approach, eliminating introspection bans and restoring most banned opcodes while retaining community-requested features and structural enhancements enabled by the EOF container.

Banned opcodes are `JUMP`, `JUMPI`, `PC`, `SELFDESTRUCT`, and `CALLCODE` for the same reasons they were banned in Minimal EOF

The EIP list consists of:

* [EIP-3540](https://eips.ethereum.org/EIPS/eip-3540): EOF - EVM Object Format v1
* [EIP-3670](https://eips.ethereum.org/EIPS/eip-3670): EOF - Code Validation
* [EIP-4200](https://eips.ethereum.org/EIPS/eip-4200): EOF - Static relative jumps
* [EIP-4750](https://eips.ethereum.org/EIPS/eip-4750): EOF - Functions
* [EIP-5450](https://eips.ethereum.org/EIPS/eip-5450): EOF - Stack Validation
* [EIP-7698](https://eips.ethereum.org/EIPS/eip-7698): EOF - Creation transaction
* [EIP-663](https://eips.ethereum.org/EIPS/eip-663): SWAPN, DUPN and EXCHANGE instructions
* [EIP-6206](https://eips.ethereum.org/EIPS/eip-6206): EOF - JUMPF and non-returning functions
* [EIP-7480](https://eips.ethereum.org/EIPS/eip-7480): EOF - Data section access instructions
* [EIP-7620](https://eips.ethereum.org/EIPS/eip-7620): EOF Contract Creation

This formulation preserves proposed EOF layout while maximizing Complete EOF’s intended features. It sheds complexity by excluding opcodes tied to symbolic introspection and gas introspection replacements.

### (D) - Introspecting EOF

Introspecting EOF represents the least possible change from Complete EOF to respond to community criticism around the opcode bans and inline solidity blocks. The Gas Introspection theme would be dropped completely and the Code Introspection theme would be rolled back to only banning creates from memory, restoring the `EXTCODE\*` opcodes and removing restrictions on introspecting code in an EOF container.

Banned opcodes are limited to `JUMP`, `JUMPI`, `PC`, `SELFDESTRUCT`, and `CALLCODE`, but also adding `CREATE`, and `CREATE2`. The `CREATE`/`CREATE2` bans uphold a lightweight version of the "Ban Code Introspection" theme, preventing runtime code generation from memory while allowing introspection of EOF containers via `EXTCODE*`. Other bans align with Minimal EOF’s rationale: removing dynamic jumps for stack validation and phasing out deprecated opcodes.



The EIP list consists of:

**eof-devnet-0**

* [EIP-3540](https://eips.ethereum.org/EIPS/eip-3540): EOF - EVM Object Format v1
* [EIP-3670](https://eips.ethereum.org/EIPS/eip-3670): EOF - Code Validation
* [EIP-4200](https://eips.ethereum.org/EIPS/eip-4200): EOF - Static relative jumps
* [EIP-4750](https://eips.ethereum.org/EIPS/eip-4750): EOF - Functions
* [EIP-5450](https://eips.ethereum.org/EIPS/eip-5450): EOF - Stack Validation
* [EIP-6206](https://eips.ethereum.org/EIPS/eip-6206): EOF - JUMPF and non-returning functions
* [EIP-7480](https://eips.ethereum.org/EIPS/eip-7480): EOF - Data section access instructions
* [EIP-663](https://eips.ethereum.org/EIPS/eip-663): SWAPN, DUPN and EXCHANGE instructions
* [EIP-7620](https://eips.ethereum.org/EIPS/eip-7620): EOF Contract Creation
* [EIP-7698](https://eips.ethereum.org/EIPS/eip-7698): EOF - Creation transaction

**eof-devnet-1**

* [EIP-7873](https://eips.ethereum.org/EIPS/eip-7873): EOF - TXCREATE and InitcodeTransaction type
* Remove EIP-7698


`EXTCODEADDRESS`, `EXTCODETYPE` and `PAY` are not necessary in this option. The metadata section would be visible to `COPDECOPY` and is no longer necessary.

### Comparison Table

### EIP Inclusion by EOF Option

| EIP Number                                          | Description                                 | Complete EOF<br>(15 EIPs) | Minimal EOF<br>(6 EIPs) | Baseline EOF<br>(10 EIPs) | Introspecting EOF<br>(10 EIPs) |
| --------------------------------------------------- | ------------------------------------------- |:-------------------------:|:-----------------------:|:-------------------------:|:------------------------------:|
| [EIP-3540](https://eips.ethereum.org/EIPS/eip-3540) | EOF - EVM Object Format v1                  |    :heavy_check_mark:     |   :heavy_check_mark:    |    :heavy_check_mark:     |       :heavy_check_mark:       |
| [EIP-3670](https://eips.ethereum.org/EIPS/eip-3670) | EOF - Code Validation                       |    :heavy_check_mark:     |   :heavy_check_mark:    |    :heavy_check_mark:     |       :heavy_check_mark:       |
| [EIP-4200](https://eips.ethereum.org/EIPS/eip-4200) | EOF - Static relative jumps                 |    :heavy_check_mark:     |   :heavy_check_mark:    |    :heavy_check_mark:     |       :heavy_check_mark:       |
| [EIP-4750](https://eips.ethereum.org/EIPS/eip-4750) | EOF - Functions                             |    :heavy_check_mark:     |   :heavy_check_mark:    |    :heavy_check_mark:     |       :heavy_check_mark:       |
| [EIP-5450](https://eips.ethereum.org/EIPS/eip-5450) | EOF - Stack Validation                      |    :heavy_check_mark:     |   :heavy_check_mark:    |    :heavy_check_mark:     |       :heavy_check_mark:       |
| [EIP-6206](https://eips.ethereum.org/EIPS/eip-6206) | EOF - JUMPF and non-returning functions     |    :heavy_check_mark:     |                         |    :heavy_check_mark:     |       :heavy_check_mark:       |
| [EIP-663](https://eips.ethereum.org/EIPS/eip-663)   | SWAPN, DUPN and EXCHANGE instructions       |    :heavy_check_mark:     |                         |    :heavy_check_mark:     |       :heavy_check_mark:       |
| [EIP-7069](https://eips.ethereum.org/EIPS/eip-7069) | Revamped CALL instructions                  |    :heavy_check_mark:     |                         |                           |                                |
| [EIP-7480](https://eips.ethereum.org/EIPS/eip-7480) | EOF - Data section access instructions      |    :heavy_check_mark:     |                         |    :heavy_check_mark:     |       :heavy_check_mark:       |
| [EIP-7620](https://eips.ethereum.org/EIPS/eip-7620) | EOF Contract Creation                       |    :heavy_check_mark:     |                         |    :heavy_check_mark:     |       :heavy_check_mark:       |
| [EIP-7698](https://eips.ethereum.org/EIPS/eip-7698) | EOF - Creation transaction                  |            devnet only             |   :heavy_check_mark:    |    :heavy_check_mark:     |               devnet only                |
| [EIP-7873](https://eips.ethereum.org/EIPS/eip-7873) | EOF - TXCREATE and InitcodeTransaction type |    :heavy_check_mark:     |                         |                           |       :heavy_check_mark:       |
| [EIP-7834](https://eips.ethereum.org/EIPS/eip-7834) | Separate Metadata Section for EOF           |    :heavy_check_mark:     |                         |                           |                                |
| [EIP-7761](https://eips.ethereum.org/EIPS/eip-7761) | EXTCODETYPE instruction                     |    :heavy_check_mark:     |                         |                           |                                |
| [EIP-7880](https://eips.ethereum.org/EIPS/eip-7880) | EOF - EXTCODEADDRESS instruction            |    :heavy_check_mark:     |                         |                           |                                |
| [EIP-5920](https://eips.ethereum.org/EIPS/eip-5920) | PAY opcode                                  |    :heavy_check_mark:     |                         |                           |                                |
## Development

### (A) - Complete EOF

While Complete EOF’s design is complete, development remains ongoing. A [testnet plan](https://notes.ethereum.org/@ipsilon/eof-testnets-plan) outlines two additional iterations: one incorporating all backward-incompatible changes, followed by another focusing on "additive" opcodes accommodating introspection bans.

A key unresolved issue is deploying contracts during a fork transition. This is a question raised by [EIP-7666: EVM-ify the Identity Precompile](https://eips.ethereum.org/EIPS/eip-7666) independent of EOF. It would deploy a short contract at address `0x4` to replace a simple precompile. This would be a precursor to eventually removing all other precompiles once features like EVMMAX are delivered. Complete EOF would leverage this to deploy an EOF-specific creator contract. With this Complete EOF contracts would not use the standard contract creation process but instead would rely on a predeployed contract to serve as a toehold `TXCREATE` factory.

### (B) - Minimal EOF

This option has a high impact on client development. Previously banned opcodes would be re-enabled in code verification. Features from eof-devnet-0, such as structural support for `EOFCREATE`, would be removed. New paths for EOF contract creation would require specification and implementation, with revisions to `CREATE` and `CREATE2` to detect and verify EOF code and initcode outputs.

### \(C\) - Baseline EOF

Baseline EOF involves the least deviation from eof-devnet-0. It re-exposes banned opcodes, allows them in validation, and removes operations tied to code and gas introspection bans. New development is limited to updating the create paths and determining if `EOFCREATE` paths should be used for initcode or if a unified `CREATE`/`CREATE2` path should be used.

### (D) - Introspecting EOF

Introspecting EOF strikes a practical balance in client development effort, sitting between Complete EOF and Baseline EOF. It requires implementing `TXCREATE`, the InitcodeTransaction type, and an EOF creation precompile—mirroring Complete EOF’s deployment strategy—but avoids the broader tooling impacts tied to gas and full code introspection bans. Restoring `EXTCODE*` opcodes reduces the need for new introspection substitutes, simplifying validation logic compared to Complete EOF. This responds to client team feedback by minimizing disruption while advancing EOF’s core goals.

Unlike Baseline EOF, which leans on existing devnet-0 progress, Introspecting EOF demands new work for `TXCREATE` integration, but this is offset by leveraging existing EOF container parsing already in client codebases. For teams that have prepared for Osaka’s Complete EOF's rollout, Introspecting EOF offers a viable pivot with moderate adjustments, avoiding the full unwind required by abandoning EOF entirely.


### Other alternatives

It needs to be noted that adopting other alternatives is not an option without work. Most clients have integrated EOF-supporting code in recent releases for the anticipated Osaka rollout. Each client must remove these code paths and unwind architectural changes for code validation and parsed structures.

## Developer Tooling

### (A) - Complete EOF

Complete EOF does not deprecate legacy EVM options, so tools targeting legacy EVM remain largely unaffected—true for all four options. However, Complete EOF code cannot be introspected by the EVM, limiting onchain tools to symbolic contract views onchain, though off-chain tools like JSON-RPC and chain analysis retain full visibility.

Tools reading EOF code will need to be able to parse the EOF container and add new opcodes added by EOF. Unless the aim of the tool is to assert the validity of the EOF code these tools will not need to implement validation and verification logic. It is expected that most major development language would develop libraries to provide this parsing and verification, as is common with other VM systems.


### (B) - Minimal EOF

Like Complete EOF there is no impact on code targeting legacy code.

There will be a smaller impact on code generating tools targeting an EOF container. While the same work to generate and validate the EOF container remain, the ability to support nearly all the existing opcodes remains, and will present a much smaller incompatibility surface. inline assembly using `JUMP` and `JUMPI` can be easily migrated to relative jumps, unless the assembly depends on dynamic jumps. The `RJUMPV` operation is a good alternative to dynamic jumps for operations such as function dispatch. The `PC` operation can be emulated with a static `PUSH` instruction, and code sections provide subroutine functionality. Code depending on `CALLCODE` and `SELFDESTRUCT` should re-write their logic to not depend on these deprecated opcodes.

It is expected that code generated with Minimal EOF would retain most of the gas and size reductions observed in experimental Solidity forks. This is because most of the reductions come from static jumps not requiring `JUMPDEST` and `JUMP` operations typically costing one byte less for jumps targeting anything past the first 256 bytes of the contract. Size reductions from the `EXTCALL` opcodes would not be realized, but such opcodes are not as frequent as the `JUMP` series opcodes.

Tools reading EOF will have mostly the same impact as in Complete EOF, except that EOF code can easily be introspected by onchain code.

### \(C\) - Baseline EOF

Baseline EOF has the same impact as Minimal EOF on developer tooling, except new opcodes that would need to be supported by generation and reading tools.

The gas and size impacts may be marginally better than Minimal EOF with the addition of the `JUMPF` opcode enabling tail calls, but those will be dwarfed by the gains from the `RJUMP` opcodes.

### (D) - Introspecting EOF

"Introspecting EOF aligns closely with Complete EOF for deployment tooling, relying on InitcodeTransaction as the primary contract creation mechanism—a shift manageable with library updates (e.g., Ipsilon’s planned EOF parsing tools for Solidity/Vyper). For inline assembly, impacts are moderate: `CREATE` and `SELFDESTRUCT` use cases require refactoring, but restored `EXTCODE*` opcodes preserve compatibility with existing introspection patterns, addressing concerns about breaking deployed contracts. This makes Introspecting EOF more palatable than Complete EOF for developers, with a smaller incompatibility surface than its full-ban counterpart.

Gas and size savings are very similar to Baseline EOF, with the only differences emerging from the create paths.

## Testing

Testing will focus on the EEST testing that is specific to EOF features. The EEST team recently committed a PR that processed the entire legacy JSON testing suite and converted compatible tests into EOF form.

When I speak of testing suited being "done" I mean in a sense that manual test development based on reading the spec will not produce many more significant tests (if any), and that other techniques such as fuzzing and code coverage analysis of clients should be used to inform the development of new tests. Any program can have more tests written, it is an engineering question of effort to reward ratio to decide where to focus testing efforts.

### (A) - Complete EOF

The testing suite for eof-devnet-0 is mostly done. Development for eof-devnet-1 features have not begun as there are no EEST tests implemented for new features. It is expected `TXCREATE` and the InitcodeTransaction would require the most testing. The InitcodeTransaction would require both EEST test support and transaction fuzzing support. `EXTCODEADDRESS`, and `EXTCODETYPE`, and `PAY` would have a smaller footprint and would only require testing in EEST.

### (B) - Minimal EOF

The testing suite for eof-devnet-0 covers more features than exist in Minimal EOF. There would need to be updates to the suite to cover new contract creation paths, but they are similar to existing negative tests that verify those paths don't work. Minor work will be needed to remove unsupported EOF features that were written for eof-devnet-0 and to update valid opcodes in code verification tests.

### \(C\) - Baseline EOF

The testing suite for Baseline EOF is similar to Minimal EOF, except there would be less testing code removed. Similar work to verify create paths and the unbanned opcodes would also be performed.

### (D) - Introspecting EOF

Testing for Introspecting EOF lands between Complete EOF and Baseline EOF in scope. The eof-devnet-0 suite covers most base features, but new tests are needed for `TXCREATE` and the precompiled deployment path—mirroring Complete EOF requirements. Tests previously asserting `EXTCODE*` opcodes failure will need to be adapted to validate they function correctly without bans. Such tests will be easily adaptable from legacy testing.

## Comparison Chart

|                                          | Complete EOF       | Minimal EOF        | Baseline EOF       | Introspecting EOF  |
|------------------------------------------|--------------------|--------------------|--------------------|--------------------|
| EIPs                                     | 15                 | 6                  | 10                 | 10                 |
| Opcodes Banned                           | 16                 | 5                  | 5                  | 7                  |
| Total Opcodes Added                      | 23                 | 5                  | 16                 | 16                 |
| Code/Data Separation                     | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Data Section Opcodes                     | :heavy_check_mark: | :x:                | :heavy_check_mark: | :heavy_check_mark: |
| Factory Contract/Data Separation         | :heavy_check_mark: | :x:                | :heavy_check_mark: | :heavy_check_mark: |
| Tail Calls                               | :heavy_check_mark: | :x:                | :heavy_check_mark: | :heavy_check_mark: |
| Deep SWAP/DUP                            | :heavy_check_mark: | :x:                | :heavy_check_mark: | :heavy_check_mark: |
| Gas Introspection Ban                    | :heavy_check_mark: | :x:                | :x:                | :x:                |
| EXTCODE* introspection   Ban             | :heavy_check_mark: | :x:                | :x:                | :x:                |
| CREATE introspection Ban                 | :heavy_check_mark: | :x:                | :x:                | :heavy_check_mark: |
| Solidity Inline `assembly` Compatibility | Moderate           | High               | High               | High               |
| Code Impacts from eof-devnet-0           | Medium             | High               | Low                | Low                |

## Conclusion

EOF represents years of EVM evolution in one fork, offering tools to enhance performance and flexibility—its path in Fusaka hinges on aligning ambition with practicality. This document presents four options, each shaped by community input to address diverse priorities. Complete EOF offers a sweeping redesign with 15 EIPs, delivering robust optimizations at the cost of heightened complexity. Minimal EOF provides a streamlined, Shanghai-inspired approach with 6 EIPs, prioritizing compatibility over advanced features. Baseline EOF strikes a middle ground with 10 EIPs, retaining key enhancements while minimizing deviation from existing devnet progress. Introspecting EOF delivers a practical evolution with 11 EIPs, retaining much of the vision of the code introspection changes while restoring `EXTCODE*` and gas opcodes to ease ecosystem friction, offering a balanced path for Fusaka’s execution layer. Each option carries unique implications for client teams, developer tools, and testing efforts, as detailed in this analysis.

By choosing a unified direction we can ensure that Ethereum’s execution layer evolves with purpose and resilience to meet future demands.
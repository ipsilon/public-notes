---
title: "PEEPanEIP-3855: PUSH0 Instruction"
type: slide
slideOptions:
  theme: league
  allottedMinutes: 15
---

### Ipsilon

Hugo de la Cruz Romero -- [github.com/hugo-dc](https://github.com/hugo-dc)

Alex Beregszaszi [github.com/axic](https://github.com/axic)

--

A protocol research team focusing on the EVM. Website: [ipsilon.xyz](https://ipsilon.xyz)

---

### EIP-3855: PUSH0 Instruction

- Status: Review
- Considered for inclusion in <br>_Shanghai_ Network Upgrade

---

### Introduction

- Usage of constant value `0`
    - Value of zero frequently used by many instructions expecting offsets as inputs.

---

### Introduction

- Examples:
    - Opcodes dealing with offsets:
        - `CALLDATACOPY`
        - `CODECOPY`
        - `EXTCODECOPY`
        - etc.
    - `CALL*`: Setting zeros as return data parameters when the contract prefers using `RETURNDATA*` to retreive the results.

---

### Abstract

- New opcode `PUSH0` with a cost of `2` gas (`base`). 
- It is introduced as the instruction number `0x5f`.

|Hex|Name|
|---|----|
|...|... |
|**`0x5f`**| **PUSH0**|
|`0x60`| PUSH1|
|...   | PUSHn|
|`0X7f`| PUSH32|

---

### Motivation

- Reduce gas usage and contract code size:
    - `PUSH1 00` ~ `60 00`: 400 gas for deployment, 3 gas for runtime.
    - `PUSH0` ~ `5f`: 200 gas for deployment, 2 gas for runtime.
    

---

### Motivation

- Reduce risk of contracts (mis)using instructions as an optimisation measure.
    - `PC`
    - `MSIZE`
    - `CALLDATASIZE`
    - `RETURNDATASIZE`
    - `CODESIZE`
    - `CALLVALUE`
    - `SELFBALANCE`

---

### Example

- EIP-2733 (*Withdrawn*)
    - If implemented, would have changed the behaviour of `RETURNDATASIZE` such that it may not be guaranteed to be zero at the beginning of the call frame.

---

### Analysis and Findings


---

### Analysis and Findings

- Traced transactions in two different nodes syncing mainnet:
    - Geth Node 1: Syncing at block ~8M
    - Geth Node 2: Syncing at block ~12M

---

### Analysis and Findings

- Result: Histogram of Opcode usage, with special column for `PUSH1 00` (`PUSH0`):

![](https://storage.googleapis.com/ethereum-hackmd/upload_6dfbcd92e97acc3630b1113e7d743a8e.png)


![](https://storage.googleapis.com/ethereum-hackmd/upload_1f96789b3db77b30d170b68fb55d7737.png)

![](https://storage.googleapis.com/ethereum-hackmd/upload_6a39ac8cee0a3f0faa29be02ea328037.png)


---

#### General Opcode Usage

![](https://storage.googleapis.com/ethereum-hackmd/upload_7419f3867b20298e665e65889561887d.png =750x580)

---

#### PUSHn Usage

![](https://storage.googleapis.com/ethereum-hackmd/upload_49e5f844744772f73602bb94e06e3d82.png =750x580)

---

#### PUSH1 00

![](https://storage.googleapis.com/ethereum-hackmd/upload_129a9d38c95bdd18d3d3270f42aba943.png =750x580)

---

#### Other commonly used constants

![](https://storage.googleapis.com/ethereum-hackmd/upload_f838cd889445229b39b874c35b6d89c5.png =650x550)

---

### Commonly used constants

- `0`: solved by `PUSH0`
- `1`: largely used in loops, could be solved by `INC`/`DEC`
- `2`: mostly used by old contracts to emulate shifts
- `31` and `32`: are mostly used by the ABI en/decoder in Solidity

---

### `INC`/`DEC`

- `INC` to increase-by-one the top item, `DEC` to decrease
- Useful for loops, where this is done with `PUSH1 1 ADD`
- Pro: saves 2x200 gas for deployment, and at least costs half runtime
- Con: too much of a CISC direction
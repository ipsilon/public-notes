---
title: "PEEPanEIP-3860: Limit and meter initcode"
type: slide
slideOptions:
  theme: league
  allottedMinutes: 30
---

### Ipsilon

Pawel Bylica -- [github.com/chfast](https://github.com/chfast)

--

A protocol research team focusing on the EVM. Website: [ipsilon.xyz](https://ipsilon.xyz)

---

## EIP-3860: <br>Limit and meter initcode

- Status: Review
- Considered for inclusion in <br>_Shanghai_ Network Upgrade

---

### Abstract

- _initcode_ max length limited to `49152` bytes
- _initcode_ costs `2` gas per 32-byte chunk

---

### _Code_ vs _Initcode_

- Code: EVM bytecode of a deployed contract (what is stored in Ethereum State)
- Initcode: ephemeral EVM bytecode used as a contract _constructor_

---

### Let's bound initcode length

- Code length is already bound to `24576` bytes (EIP-170)
- Initcode can be of any length
- EIP-3860 bounds it to **2x** the code length limit

---

### Let's pay for JUMPDEST analysis

- JUMPDEST analysis must be performed on any EVM bytecode before execution
- JUMPDEST analysis cost is proportional to the code length
- Initcode is ephemeral
- EIP-3860 adds cost of `2` gas per 32-byte chunk of _initcode_

---

### Why not pay <br>for contract code length?

- Already cost `200` to deploy each byte
- JUMPDEST analysis may be cached alongside the code

---

### Why this cost?

- 2 gas per 32-byte chunk
- Conservative to limit impact
- Best performing analysis: Geth
- Compared with `KECCAK256` performance 

---

### What is JUMPDEST analysis?

![](https://storage.googleapis.com/ethereum-hackmd/upload_4c48403c75664e8a912a78d5ca494742.png)


---

### Valid usage above limit

- Transaction [0x48342552bfde19b6d0ccb79850943ee891181af65efb47662ebd8f46a428fa3a](https://etherscan.io/tx/0x48342552bfde19b6d0ccb79850943ee891181af65efb47662ebd8f46a428fa3a)
- creates 11 contracts in single go
- `25.38M` gas used
- transaction with `116262` bytes of initcode
- 237% of the EIP-3860 limit

---

## Thanks!


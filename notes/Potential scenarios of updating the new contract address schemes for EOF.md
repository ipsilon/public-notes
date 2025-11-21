# Potential scenarios of updating the new contract address schemes for EOF

Everything started in a gh thread ["EOFCREATE: Don't hash the init-container #162"](https://github.com/ipsilon/eof/issues/162). The thread can be read later for full history and discussion so far.

The problem at hand is how to obtain new addresses for EOF contracts created, and more generally - which deployment methods to offer.

We have several scenarios we are thinking about. They are of wider scope (not only `EOFCREATE`), because the deployment-related issues with EOF seem to be larger than that.

The structure of the document is that there are several technical options (marked A, B, ...) mixed and matched into few scenarios (marked 0, 1a, ...) which may make sense as ways to tackle our issues. **NOTE** that any magic prefixes to avoid incidental hash collisions (e.g. with `CREATE` scheme etc.) are _not yet considered_ for reading simplicity.

## Scenario 0 - status quo

Keep everything as is specced out now:

A/ `EOFCREATE` hashes with `keccak256(sender + initcontainer_hash + salt)`
B/ Creation tx (empty `to`) with EOF container hashes with `keccak256(sender + sender_nonce)`

## Scenario 1a - push `TXCREATE`, no predeployed contract

Includes a proposed ([source and discussion](https://github.com/ipsilon/eof/issues/162#issuecomment-2491696792)) simplification to `EOFCREATE` scheme, as well as `TXCREATE` recently cut from EOFv1.

C/ `EOFCREATE` hashes with `keccak256(sender + salt)`
B/ Creation tx (empty `to`) with EOF container hashes with `keccak256(sender + sender_nonce)`
D/ `TXCREATE` hashes with `keccak256(sender + salt)`

## Scenario 1b - push `TXCREATE`, predeployed contract

Very similar to 1a above.

C/ `EOFCREATE` hashes with `keccak256(sender + salt)`
D/ `TXCREATE` hashes with `keccak256(sender + salt)`
E/ a predeployed `TXCREATE` factory contract to bootstrap EOF

## Scenario 2 - empower EOF creation txs

Similar to 1a, just avoid `TXCREATE`.

C/ `EOFCREATE` hashes with `keccak256(sender + salt)`
F/ Creation tx (empty `to`) with EOF container hashes with `keccak256(sender + initcontainer_hash + salt)`.

## Scenario 3 - only rework code witness of `EOFCREATE`

A somewhat interim result arrived at the gh thread discussion.

H/ `EOFCREATE` hashes with `keccak256(sender + code_address + salt + during_init + subcontainer_idx)`
B/ Creation tx (empty `to`) with EOF container hashes with `keccak256(sender + sender_nonce)`

## Traits of proposed features

### A/ `EOFCREATE` hashes with `keccak256(sender + initcontainer_hash + salt)`

Seems like a middle ground solution which doesn't satisfy anyone fully. Counterfactual/cross chain deployments to deterministic addresses with bytecode guarantee are possible but awkward and expensive (one puts an `EOFCREATE` into the creation tx initcontainer). Including `initcontainer_hash` violates code non-observability, because it locks in the contents of the initcontainer e.g. preventing re-writing it in some future upgrade.

Doesn't support generic factories, deployable codes are always embedded in the factory container (common for all `EOFCREATE` designs).

### B/ Creation tx (empty `to`) with EOF container hashes with `keccak256(sender + sender_nonce)`

It is the least effort approach to creation txs in EOF allowing it to bootstrap. Useless from the PoV of bytecode guarantees.

### C/ `EOFCREATE` hashes with `keccak256(sender + salt)`

([source and discussion](https://github.com/ipsilon/eof/issues/162#issuecomment-2491696792))

Puts the responsibility of including any relevant commitments into the `salt` inside the factory contract (one calling `EOFCREATE`) level. Would require compilers to either change their approach to creation or provide good defaults in cases where the user doesn't care. One such default proposed is `new Foo{salt: salt}(input)` to use `final_salt = keccak256(salt || Foo_hash || input)` as the `salt` in `EOFCREATE`, the premise being to recreate `CREATE2` guarantees ([source](https://github.com/ipsilon/eof/issues/162#issuecomment-2504296453)).

This scheme has the advantage of not trying to cater for all different factories' needs (some want `initcode` in the witness, some don't, some want `input` in the witness, some don't, some want `nonce`, some don't etc.).

Doesn't support generic factories, deployable codes are always embedded in the factory container (common for all `EOFCREATE` designs).

### D/ `TXCREATE` hashes with `keccak256(sender + salt)`

Requires a new tx type `InitcodeTransaction`.

Supports deployments to deterministic addresses with bytecode guarantee without relying on `EOFCREATE` workarounds.

Combines well with AA, that is, deployments by smart contract wallets.

Substitutes generic factories.

### E/ a predeployed `TXCREATE` factory contract to bootstrap EOF

`TXCREATE` is only allowed in EOF contracts, and only EOF contracts are allowed to deploy EOF contracts, so the "first" `TXCREATE` must be introduced inside a predeployed factory contract.

### F/ Creation tx (empty `to`) with EOF container hashes with `keccak256(sender + initcontainer_hash + salt)`.

A way to have counterfactual/cross chain deployments to deterministic addresses with bytecode guarantee without `EOFCREATE` workarounds or `TXCREATE`.

From EVM PoV, `salt` is found in such transaction's `data`, as 32 bytes between the initcontainer and calldata, and this is a bit hacky (building upon how (B/) already is hacky).

**NOTE** There is a variant which assumes EIP-7834 is included in the EOFv1 rollout and there's a metadata section in the initcontainer which can hold the salt. Hacky `salt` handling can then be dropped, but some might see putting `salt` into metadata hacky and extra additional complexity for the tooling.

### H/ `EOFCREATE` hashes with `keccak256(sender + code_address + during_init + subcontainer_idx + salt)`

([source and also discussion](https://github.com/ipsilon/eof/issues/162#issuecomment-2455824240))

**NOTE** that here `sender + code_address + during_init + subcontainer_idx` is intended to be a very granular commitment to the initcode, one which substitutes `initcontainer_hash` from (A/). This can be also done in other equivalent ways (see link above).

Removes the immediate problem with code observability, but is a very rigid hashing scheme (but it still requires including witness to input and nonce into the `salt`, if needed, on the application layer).

Doesn't help (as (A/) would) with counterfactual/cross chain deployments to deterministic addresses with bytecode guarantee.

Doesn't support generic factories, deployable codes are always embedded in the factory container (common for all `EOFCREATE` designs).


## Comparison of scenarios

This is my (PD) subjective take at this point, with some early comments factored in, please send feedback.

Legend: ❌, ⚠️, ✅ - worse, "ok but", optimal

|                                         | (0.) | (1a.) | (1b.) | (2.) | (3.) |
|-----------------------------------------|------|-------|-------|------|------|
| EOFCREATE tooling                       | ✅   | ⚠️     | ⚠️    | ⚠️   | ✅    |
| Code non-observable                     | ❌   | ✅     | ✅    | ✅   | ✅    |
| Generic factories (w/ AA compatibility) | ❌   | ✅     | ✅    | ⚠️   | ❌    |
| Bytecode guarantees                     | ⚠️   | ✅     | ✅    | ✅   | ❌    |
| Requires a predeploy                    | ✅   | ✅     | ⚠️    | ✅   | ✅    |
| Hacky                                   | ⚠️   | ⚠️     | ✅    | ❌   | ⚠️    |

### Criteria

**EOFCREATE tooling** - how much harder are we making for compilers/std libs to control `new Contract`, e.g. by requiring them to include appropriate commitments into the `salt`.
**Code non-observable** - whether we can observe on-chain code or its artifacts like hash.
**Generic factories (w/ AA compatibility)** - whether we can have a contract factory which will deploy arbitrary code (with `CREATE2` in legacy this is accomplished by sending bytecode in calldata). AA compatibility means that SC wallets can deploy contracts by using such factory.
**Bytecode guarantees** - whether counterfactual deployments can be done with bytecode guarantees (contract having known bytecode can be interacted with before creation, at a known address, without trust assumptions).
**Requires a predeploy** - whether bootstraping EOF via a predeployed contract can be avoided.
**Hacky** - ...

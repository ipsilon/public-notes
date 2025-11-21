# EOF bootstrapping

### Options

Here are the options for EOF bootstrapping which are considered in combination with `TXCREATE` opcode and the `InitcodeTransaction`, as specced in [EIP-7873](https://eips.ethereum.org/EIPS/eip-7873). Current status (EIP-7873 with legacy EVM code enabling TXCREATE opcode) is good, but there was [feedback](https://ethereum-magicians.org/t/eip-7873-eof-txcreate-and-initcodetransaction-type/22765) that some features (improvements on multichain deployments and having a default deployment method) would still be nice to have and there are alternatives to consider. The doc attempts to gather these in one spot and list their differences. 

For the reference, EIP-7873 specifies that `TXCREATE` calculates `new_address` as `keccak256(0xff || sender32 || salt)[12:]`.

**NOTE** that (1.) and (2.) are previous designs which are kept here only for reference, as they both have been superseded by the baseline (3.)

1. (\* obsolete previous design) [Creator Contract](https://github.com/ethereum/EIPs/blob/7dba7f954e7270a0cde3d269d938a574f1378fb3/EIPS/eip-7873.md#creator-contract) predeploy
2. (\* obsolete previous design) [EIP-7698](https://eips.ethereum.org/EIPS/eip-7698) - eof creation tx with `to: nil` and EOF initcontainer prepended to input calldata in `tx.data`
3. Allowing `TXCREATE` in legacy EVM, disallowing `to: nil` for InitcodeTransactions (current EIP-7873)
4. Allowing `to: nil` InitcodeTransactions doing:
      ```
      if tx.to is null:
        assert len(initcodes) == 1
        run(initcodes[0])
      ```
      Calculate `new_address` like for an ordinary `to: nil` creation tx (nonce based)
5. Allowing `to: nil` InitcodeTransactions which interprets `tx.data = <tx_initcode_hash> || <salt> || <input...>` as TXCREATE arguments. `tx.initcodes[tx_initcode_hash]` is run with `input` as input.
     Calculate `new_address` as `keccak256(0xff || initcode_hash || input || salt)[12:]` (\*\*).
6. (middle ground between (4.) and (5.)) Allowing `to: nil` InitcodeTransactions which interprets `tx.data = <salt> || <input...>` and executes
      ```
      if tx.to is null:
        assert len(initcodes) == 1
        run(initcodes[0])
      ```
    Calculate `new_address` as `keccak256(0xff || keccak256(initcodes[0]) || input || salt)[12:]`. (\*\*)

(\*\*) - `new_address` hashing scheme doesn't include `sender` as it makes it possible to make a multichain deployment independent of holding a particular private key. However, it makes it less of a default deployment method as it makes the deployment front-runnable, c.f. [similar discussion](https://github.com/ethereum/EIPs/pull/9391#issuecomment-2678451413).

### Comparison

Legend: 
 - ❌, ⚠️, ✅ - worse, "ok but", optimal
 - ➕ denote which mechanisms are employed to do the job


|                                        | (1.) (\*) | (2.) (\*) | (3.) | (4.)      | (5.) | (6.) |
|:-------------------------------------- | --------- | --------- | ---- | --------- | ---- | ---- |
| improved multichain deployment support | ✅        | ❌        | ❌   | ❌❌ (\*\*\*) | ✅   | ✅   |
| has default deployment method          | ✅        | ✅        | ❌   | ✅        | ✅   | ✅   |
|                                        |           |           |      |           |      |      |
| clean                                  | ✅        | ❌        | ⚠️   | ✅        | ❌   | ❌   |
| extendable in future forks             | ❌        | ❌        | ✅   | ❌        | ❌   | ❌   |
| easy fork transition                   | ❌        | ✅        | ✅   | ✅        | ✅   | ✅   |
| avoids contract audit                  | ❌        | ✅        | ✅   | ✅        | ✅   | ✅   |
| trivial testing                        | ❌        | ❌        | ✅   | ❌        | ❌   | ❌   |
|                                        |           |           |      |           |      |      |
| Creator Contract predeploy             | ➕        |           |      |           |      |      |
| Create via existing TX types (0,1,2)   |           | ➕        |      |           |      |      |
| TXCREATE as a legacy opcode            |           |           | ➕   |           |      |      |
| InitcodeTx (type 6) `to: nil`          |           |           |      | ➕        | ➕   | ➕   |
| `tx.data` parsing for `to: nil`        |           | ➕        |      |           | ➕   | ➕   |

Features:

- **improved multichain deployment support** - improved over legacy EVM as of Pectra.
  (\*\*\*) - (4.) might actually be worse off compared to legacy EVM, as it will require deployments of EOF via the InitcodeTransaction, at the same time leaving us with only nonce-based deployments there, meaning [Nick's method](https://medium.com/patronum-labs/nicks-method-ethereum-keyless-execution-168a6659479c) would be unavailable for multi-chain deploys (only method using a dedicated private key would work). TODO: confirm that this is the case.
- **has default deployment method** - there is an EOF deployment method which works from first Osaka block, no ERC required

"Niceness" qualities:

- **clean** - subjective feel about the amount of hacks and non-standard handling required. Things we don't like are parsing of creation args (initcontainer, salt, hash etc) from `tx.data` and also somewhat the TXCREATE leaking to legacy
- **extendable in future forks** - whether we can introduce features (multichain, default deployment method etc.) with an elegant additive change in the future
- **easy fork transition** - basically ❌ when handling the predeploy
- **avoids contract audit** - avoid having to audit a Creator Contract to include it in a fork upgrade as a predeploy
- **trivial testing** - basically ✅ for (3.) legacy TXCREATE, where you just have same tests as for "regular" TXCREATE


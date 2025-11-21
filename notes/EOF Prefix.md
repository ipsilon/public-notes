# EOF Prefix

[toc]

This document describes the process of choosing *magic* sequence of Ethereum Object Format, that will allow to distinguish future EOF contracts from any of the already deployed contracts, as described in the [Motivation section of EIP-3541](https://eips.ethereum.org/EIPS/eip-3541#motivation).

## Chain readiness

|   | Name        | [EIP-3541] activation      | № EF-prefixed code |
| - | ----------- | -------------------------- | ------------------ |
| ✅ |Ropsten     | [London] / June 24, 2021 | 0 |
| ✅ | Goerli      | [London] / June 30, 2021 | 0 |
| ✅ | Rinkeby     | [London] / July 7, 2021 | 0 |
| ✅ | **Mainnet** | [London] / August 5, 2021 | **3** |
| ✅ | Kovan       | [London] / August 12, 2021 | 0 |
| ✅ | Sepolia     | Genesis | 0 |
| ⌛ | Ethereum Classic | [ECIP-1104] / TBD     | ? |
| ⌛ | xDai       | Due next update | ? |
| ❔ | Optimism   | n/a | 0* |
| ❔ | Arbitrum   | n/a | 0* |
| ❓ | Polygon    | London / ? | ? |

#### Special note about Arbitrum & Optimism

These chains employ a verification process (and/or guarded/phased launch), which means failing code can not be deployed.  Any code starting with the `EF` byte will fail to execute on EVM and so should not exist on these chains.

### EF-prefixed legacy code

After the activation of [EIP-3541], analysis of deployed code prefixes was performed on Mainnet and all public testnets:
1. For chains having geth support, *geth snapshots* were used to collect data ([snap2code-prefixes.py](https://github.com/ipsilon/eof/blob/main/code_prefixes/snap2code-prefixes.py)). Additionally, Erigon team has verified data for Mainnet independently.
2. For chains having only OpenEthereum support *warp snapshots* were used to collect data ([warp2code-prefixes.py](https://github.com/ipsilon/eof/blob/main/code_prefixes/warp2code-prefixes.py)).

We have only found existing contracts with the `EF` byte on **Mainnet**:

- `EFF09f918bf09f9fa9` [0x6e51d4d9be52b623a3d3a2fa8d3c5e3e01175cd0](https://etherscan.io/address/0x6e51d4d9be52b623a3d3a2fa8d3c5e3e01175cd0)
- `EF` [0x897da0f23ccc5e939ec7a53032c5e80fd1a947ec](https://etherscan.io/address/0x897da0f23ccc5e939ec7a53032c5e80fd1a947ec)
- `EF` [0xca7bf67ab492b49806e24b6e2e4ec105183caa01](https://etherscan.io/address/0xca7bf67ab492b49806e24b6e2e4ec105183caa01)

## EOF prefix selection

[EIP-3540] proposes an EOF bytecode to start with the EOF prefix and version number:
```
format, magic, version
```
where `format` is `0xEF`, `version` is a single byte with version number, and `magic` may be a sequence of bytes (possibly 0-length) making the prefix unique among already deployed "legacy" bytecodes.

Next we explain two options based on the collected data.

### Two-byte prefix (`EF00`)

The only two-byte sequence starting with `EF` existing in analysed chains is `EFF0`, therefore to guarantee that none of already existing contracts is recognized as EOF, magic can be chosen to be a single byte with any value except `F0`, and we simply choose `00`.

```python
def is_eof(code):
    return len(code) >= 2 && code[0] == 0xEF && code[1] == 0x00

def get_eof_version(code):
    assert is_eof(code)
    return code[2]  # SHOULD be safe for deployed code.
```

### One-byte prefix (`EF`, no magic)

We also have an option to skip magic completely with some additional spec complexity. In this case the version byte follows `EF` directly and there are no magic bytes. But not every contract starting with `EF` is considered EOF (the ones starting with `EFF0` must never be). Additionally, we have to forbid to use `F0` as version forever.

```python
known_versions = [0x01]  # version 0xf0 must never be assigned

def is_eof(code):
    return len(code) >= 2 && code[0] == 0xEF && code[1] in known_versions

def get_eof_version(code):
    assert is_eof(code)
    return code[1]
```

## Conclusion

Based on the above we suggest to go with the two-byte prefix option, as it is the cleanest solution not requiring special cases.

[EIP-3540]: https://eips.ethereum.org/EIPS/eip-3540
[EIP-3541]: https://eips.ethereum.org/EIPS/eip-3541
[London]: https://github.com/ethereum/execution-specs/blob/master/network-upgrades/mainnet-upgrades/london.md
[ECIP-1104]: http://ecips.ethereumclassic.org/ECIPs/ecip-1104


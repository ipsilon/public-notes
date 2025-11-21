# Osaka Testnets Plan

:::success
"plans are useless but planning is indispensable" - Dwight D. Eisenhower
:::

## Osaka-0 "Pectra"

Theme is to ship what we had at the Pectra split, but merged against "main" in each client.

* All EIPs as speced when they were cut from pectra
* Depends on a stable Pectra base

exit criteria
✅ 3+ clients pass all EELS tests
✅ 3+ clients stay in sync in test net
✅ 3+ clients fuzz at trace level for 24 hours
✅ 3+ clients fuzz container formatting for 24 hours
✅ 3+ clients that pass all of the above in the same git commit

network - https://eof-devnet-0.ethpandaops.io/
(Launched 13 Feb 2025, live as of 24 Feb 2025)


## Osaka-1 "Lockdown"

Theme is to correct ergonomic issues and fine tune the old Pectra spec. If we shiped Osaka-0 in pectra and then there was a "EOF quirks" talk at devcon, these changes would address issues raised in that hypothetical talk.  Mostly covering items here - [ipsilon/eof#165](https://github.com/ipsilon/eof/issues/165)

We will also introduce all final breaking changes (such as removing EIP-7698). After this devnet stabalizes further breaking changes will require a security or similar impact rationale.

* *Add TXCREATE [EIP-7873](https://eips.ethereum.org/EIPS/eip-7873)*
* *Remove contract creation transaciton [EIP-7698](https://eips.ethereum.org/EIPS/eip-7698)*
* Modify type section to have "additional stack" instead of total stack
* Change EOFCREATE Hashing
* Change EOFCREATE stack order to match EXTCALL
* Any other stack arg re-ordering to align with legacy or other EOF ops
* Change data section index from 0x04 to 0xff
    * Retire the 0x04 header ID.
* ~~Change CREATE/CREATE2 to an exceptional halt when seeing EOF (was fails and returns 0)~~

exit criteria
[ ] 3+ clients pass all EELS tests
[ ] 3+ clients stay in sync in test net
[ ] 3+ clients fuzz at trace level for 24 hours
[ ] 3+ clients fuzz container formatting for 24 hours
[ ] 3+ clients that pass all of the above in the same git commit

Targeting around the mainnet fork activation.

Once completed compilers should target this as a "stable base" (except for EIP-7620 contract creation).  Barring any security related or existential findings this portion of the spec will be frozen.


## Osaka-2 to Osaka-N - Value add features

Theme is to add features that were cut for Pectra, and add some missing items identified by the community. This is a priority list of items to add, either in one devnet or multiple devnets. Based on external scheduling decisions some of these features may be cut from Osaka (hence the ordered list).

* ~~Add TXCREATE [EIP-7873](https://eips.ethereum.org/EIPS/eip-7873)~~
* ~~Remove contract creation transaciton [EIP-7698](https://eips.ethereum.org/EIPS/eip-7698)~~
* Add Metadata Section [EIP-7834](https://eips.ethereum.org/EIPS/eip-7834)
* Add EXTCODETYPE [EIP-7761](https://eips.ethereum.org/EIPS/eip-7761) - to differentiate eof from legacy contracts
* Add EXTCODEADDRESS [EIP-7880](https://eips.ethereum.org/EIPS/eip-7880) - to detect delegtes programatically

Exit Criteria
[ ] 4+ clients pass all EELS tests
[ ] 4+ clients stay in sync in test net
[ ] 4+ clients fuzz at trace level for 48 hours
[ ] 4+ clients fuzz container formatting for 48 hours
[ ] 4+ clients fuzz transaction formatting for ?? hours
[ ] 4+ clients that pass all of the above in the same git commit
[ ] 1 or more compilers produce EOF contracts

Osaka-2 Targeting Q2 - April/May
scoping will be determiend closer to the fork.

## Fusaka-0

No changes, just merge testnet with PeerDAS
Osaka-1 or -2 could also be a good Fusaka-0


## Fusaka Targets of opportunity, not required for V1

These could be added to Fusaka, but don't need to be.  Based on schedule desires they appear to be out.

* [EIP-7830](https://eips.ethereum.org/EIPS/eip-7830) - Contract Size Limit Increaese for EOF
* [EIP-7819](https://eips.ethereum.org/EIPS/eip-7819) - CREATEDELEGATE opcode (7702 for smart contracts)


## Out of Fusaka Scope

These may or may not be adoped, but will not be part of Fusaka.

* EXTDATACOPY / EXTDATASIZE opcodes - This may or may not be needed, so we will wait at least one fork cycle to determine if their absence is problematic
* [EIP-7701: Native Account Abstraction with EOF](https://eips.ethereum.org/EIPS/eip-7701) - The EIP needs some tuning and would benefit from being in a generalized "Account Abstraction" fork.
* Tweaks to Gas Introspection. There has been feedback that gas introsection is useful for anti-abuse/anti-spam measures. How this will be addressed will be determined by what happens post-osaka. The design could remain as-is, opcodes could be restored (just GAS, or possibly GAS + CALL/DELEGATECALL/STATICCALL), or another alternative solution may be proposed.

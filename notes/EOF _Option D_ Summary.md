# EOF "Option D" Summary

## Summary

* The `EXTCODECOPY`, `EXTCODEHASH`, and `EXTCODESIZE` opcodes are allowed within EOF containers. Their behavior is the same as when called from outside a container.
* Restrictions on creating EOF contracts from existing facilities remains, with `EOFCREATE` and `TXCREATE` opcodes providing contract creation services.
* The entire "Ban Gas Introspection" theme is removed. The `CALL`, `STATICCALL`, `DELEGATECALL`, and `GAS` opcodes are allowed within EOF containers. `CALLCODE` is not allowed as it is a deprecated instruction.
<!--* The restriction on EOF contracts `DELEGATECALL`ing non-EOF contracts remains, with the same rationale.-->


::: spoiler Summary of EOF opcode changes

The list of opcodes not permitted in EOF contracts now consists of 9 instructions, down from 16.

* `CODESIZE` and `CODECOPY` instructions, as described in EIP-3540
* `CALLCODE` and `SELFDESTRUCT` instructions, as described in EIP-3670
* `JUMP`, `JUMPI`, and `PC` instructions, as described in EIP-4200
* `CREATE` and `CREATE2` instructions, as described in EIP-7620

The list of opcodes exclusive to EOF containers now consists of 15 opcodes, down from 19.

* `SWAPN`, `DUPN`, and `EXCHANGE` instructions, as described in EIP-663
* `RJUMP`, `RJUMPI`, and `RJUMPV` instructions, as described in EIP-4200
* `CALLF` and `RETF` instructions, as described in EIP-4750
* `JUMPF` instruction, as described in EIP-6209
* `DATALOAD`, `DATALOADN`, `DATASIZE`, and `DATACOPY` instructions, as described in EIP-7480
*  `EOFCREATE` and `RETURNCODE` instruction, as described in EIP-7620

One opcode added to code in and not in an EOF container: 

*  `TXCREATE` instruction, as decribed in EIP-7873
:::

## Changes to EIPs

There is a small [PR implementing these changes](https://github.com/ethereum/EIPs/pull/9702/files?diff=unified&w=1).

* EIPs 7069, 7698, 7761, 7880 are moved from their present state in Fusaka to "DFI"
* EIP-3540 
  * Remove the ban of the `EXTCODESIZE`, `EXTCODECOPY`, `EXTCODEHASH`, and `GAS` opcodes from code in an EOF container.
  * The behavior of the `EXTCODE*` series of opcodes is extended to code in an EOF container, with all the same semantics.
* EIP-7480 updates a rationale section to refer to new behavior, but otherwise remains the same.
* EIP-7620 updates the motivation to align with new behavior, but otherwise remains the same.

## Changes to Testing Code

* eofwrap will need to be updated to include the previously restricted opcodes as valid opcodes
* eofwrap results will be inspected to ensure all existing tests for `*CALL` series opcodes are ported over to EOF.
* eofwrap results will be inspected to ensure all existing tests for `EXTCODE*` series opcodes are ported over to EOF.
* Tests for `EXTCODETYPE` and `EXTCODEADDRESS` will not be written.
* Most EIP-7069 tests for `EXT*CALL` tests will be retired. Tests that validate they do not work in plain bytecode will be extended to cover EOF containers.
* Gas testing will be added to the new EOF opcodes
  * Existing EOF gas testing will be removed, as the tests are complex.

<!--Estimated at 12 to 32 hours of work. -->

## Changes to Client Code

After finishing eof-devnet-1, these are the remaining changes.

* Clients **MUST** update instruction dispatch to add previously restricted instructions so they are valid in an EOF conainer.
* Clients **MUST** update other code that otherwise prevents the added operations from working inside an EOF container, if such code exists.
* Clients **MUST** remove `EXT*CALL` instructions from their instruction dispatches.
* Clients **SHOULD** remove code implementing `EXT*CALL` series opcodes and code exclusively supporting it.

Estimated 8 to 12 hours per client, once test code is delivered.

## Changes to Testnet Plan

The eof-devnet-1 plan remains as is.

The current eof-devnet-2 plan will be significanty revised.

Another devnet one to two weeks after eof-devnet-1 is launched will be launced adding the previously restricted bytecodes.  The same devnet succes standards as for devnet-1 will be used.

It is anticipated that this final eof-devnet will be complete before the May 20th fusaka-devnet-0 launch and will be included.

This nets to at least two weeks removed from EOF work, shifting the readyness of EOF protocol changes "left" by at least two weeks.

If EIP-7834 (metadata section) is moved to SFI, it would be added in a fusaka series devnet.
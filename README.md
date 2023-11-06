# Introduction

The Veda Protocol is an EVM extension protocol based on the Ordinals theory. Users can perform EVM contract operations by creating instruction content on Ordinals (similar to Brc-20). Veda instructions are abstracted into two modules: contract deployment and contract execution.&#x20;

The Veda Protocol operates in two modules: Veda-core and Veda-bvm. Veda-core is responsible for protocol data validation and input, while Veda-bvm handles protocol contract execution and provides **`eth_call`** methods.

Veda-core primarily indexes veda instructions on Ordinals, performs a series of verifications including signature and instruction legitimacy, and finally generates a tx hash. It then encodes the address into a 20-byte hex address compatible with EVM and other similar operations. The parsed data is then invoked via json rpc to trigger the execution in Veda-bvm.

Veda-bvm mainly deals with contract execution and contract state queries. When Veda-bvm receives an rpc call from Veda-core, it determines the action to be taken. If it's a contract deployment instruction, it will persistently store the contract bytecode for future external rpc calls to execute read-only contract methods. The Veda-bvm virtual machine can be considered a subset of EVM, with certain incompatible opcodes removed. This means that developers can use existing Solidity compilers to compile bvm contracts. For specific changes, refer to the bvm modification chapter. Additionally, Veda-bvm also offers an API service for external invocation of read-only contract methods.

## What Makes Veda Unique?

### Token Security

Veda can implement some functionalities that are unachievable on EVM. Since Veda-bvm executes based on Ordinals inscriptions, during contract deployment, the contract bytecode and invocation instructions can be inscribed separately, allowing for bytecode reuse. This feature is highly usable when deploying Erc-20 or other standard tokens. Trustworthy developers or Veda Org will pre-inscribe some secure standard token bytecode templates on Ordinals, covering several commonly used functionalities. Developers can select from different token templates based on the Ordinals inscription id according to their needs and deploy by inputting a series of parameters required for the token through the contract's constructor. This not only avoids manual token contract compilation but also ensures that tokens deployed through templates are sufficiently secure. Veda Org will also mark tokens deployed through secure templates on the Veda application, allowing users to fully understand the risks associated with the current token.

### Avoiding UTXO Dust Accumulation

Given that the Veda instruction set contains ample information to identify the sender of the instruction, in practice, there's no need for the wallet to actually possess the ordinals inscription. This sets the groundwork for preventing the creation of a multitude of dust UTXOs. We could establish a service that accepts inscriptions on its own, repeatedly inscribing Veda instructions on a specified UTXO every time. Although this might corrupt the inscription and even lead to negative inscriptions, Veda isn't concerned with who actually holds the inscription. Veda solely executes content indexed by ordinals chronologically based on their creation time. Any subsequent transfer activities related to the Inscription won't impact Veda's execution results. However, due to the current instability of the ordinals protocol, Veda temporarily won't index negative inscriptions, as this might disrupt the sequence of inscriptions and potentially lead to a Veda fork attack. This feature might be developed once the ordinals protocol achieves relative stability in the future.

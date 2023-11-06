# Changes Relative to EVM

## Value

In the design of the Veda protocol, the Veda-BVM is used solely as a virtual machine for contract execution. In fact, the consensus layer of Veda is not within BVM, so Veda does not have a native token. Due to the absence of a native token setting, the "value" field in transactions has also been removed.

## Gas

In the Veda protocol, the actual action of paying gas and sending transactions is carried out on the BTC chain. This means that setting up a gas mechanism in Veda-BVM is unnecessary. Since BTC's block production speed is much slower than ETH's, and there's a size limit to the content written in each block, there's no need to use the maximum gaslimit of a block to limit its execution volume. However, to prevent malicious Solidity code from being written, the BVM internally sets an upper limit for gaslimit. This limit can cover the execution of all non-malicious contract codes while also restricting the execution of malicious codes.

##

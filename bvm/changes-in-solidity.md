# Changes in Solidity

Even though Veda's goal is to seamlessly utilize the ETH development ecosystem, inevitably some incompatible global variables or global methods (such as those related to value and gas) have to be removed.

## Global Variables

### Block and Transaction Properties

* `blockhash(uint blockNumber) returns (bytes32)`: **Deleted**
* `block.basefee (uint)`: **Deleted**
* `block.chainid (uint)`: Veda's chain id
* `block.coinbase (address payable)`: **Deleted**
* `block.difficulty (uint)`: **Deleted**
* `block.gaslimit (uint)`: **Deleted**
* `block.number (uint)`: The transaction location's block number (Bitcoin)
* `block.prevrandao (uint)`: The transaction location's block nonce (Bitcoin)
* `block.timestamp (uint)`: The transaction location's block timestamp (Bitcoin)
* `gasleft() returns (uint256)`: **Deleted**
* `msg.data (bytes calldata)`: Unchanged
* `msg.sender (address)`: Unchanged, sender address is 20-byte hex address, not bitcoin wallet address
* `msg.sig (bytes4)`: Unchanged
* `msg.value (uint)`: **Deleted**
* `tx.gasprice (uint)`: **Deleted**
* `tx.origin (address)`: Unchanged

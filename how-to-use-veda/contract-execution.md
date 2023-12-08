# Contract Execution

## Example: Mint 100 ERC-20 token to address

Here is contract source code

```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.20;

import "@openzeppelin/contracts@5.0.0/token/ERC20/ERC20.sol";
import "@openzeppelin/contracts@5.0.0/token/ERC20/extensions/ERC20Burnable.sol";
import "@openzeppelin/contracts@5.0.0/access/Ownable.sol";
import "@openzeppelin/contracts@5.0.0/token/ERC20/extensions/ERC20Permit.sol";

contract ERC20Token is ERC20, ERC20Burnable, Ownable {
    constructor(
        string memory tokenName,
        string memory tokenSymbol,
        uint256 supply,
        address initialOwner
    )
        ERC20(tokenName, tokenSymbol)
        Ownable(initialOwner)
    {
        _mint(msg.sender, supply);
    }

    function mint(address to, uint256 amount) public onlyOwner {
        _mint(to, amount);
    }
}
```

We assume that the contract has already been deployed on Veda using the 'deploy' instruction. Now, our goal is to call the 'mint' method to mint 100 tokens to a specific address.

### Get public key

See [Get your wallet public key](deploy-a-contract.md#get-your-wallet-public-key).

### Get contract deploy id

The contract address in Veda instructions differs slightly from ETH. In Veda instructions stored on Ordinals, the contract's address is represented by the Ordinals id of the contract 'deploy' instruction (even though it's stored as a 20-byte hex string in veda-bvm). This means the 'contract' attribute will be represented in the form of `${deploy_tx_hash}i${offset}`. The method for generating the 20-byte hex string in Veda-bvm can be referenced in BVM Address.

### **Constructing execution data**

#### Encode function call data

```typescript
import axios from 'axios'
import Web3 from 'web3'
import { toWei } from 'web3-utils'

const getABIItemByName = (abi: string, functionName: string) => {
  return JSON.parse(abi).find((item: any) => item.type === 'function' && item.name === functionName) || null
}

const web3 = new Web3()
const abi: string = "" // You should store abi on your app.(Just like other evm application)
const callParams = {
  func: 'mint',
  args: [
    'TARGET ADDRESS HERE',
    toWei(100, 'ether'),
  ],
}
const data = web3.eth.abi.encodeFunctionCall(getABIItemByName(abi, callParams.func), callParams.args)
```

```json
{
    "p": "veda",
    "publicKey": "YOUR PUBLICKEY HERE",
    "action": "execute",
    "contractLocation": "CONTRACT DEPLOY ID HERE",
    "nonce": 0, // Wallet tx count
    "data": "YOUR FUNCTIONCALL DATA HERE"
}
```

### Generate transaction hash

Refer to [here](deploy-a-contract.md#generate-transaction-hash)

### Sign execution data

See [Sign deployment data](deploy-a-contract.md#sign-deployment-data)

### Inscribe execution data

See [Inscribe deployment data](deploy-a-contract.md#inscribe-deployment-data)

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

### Generate Contract Address

<pre class="language-typescript"><code class="lang-typescript">import * as bitcoin from 'bitcoinjs-lib'
<strong>import { keccak256 } from 'web3-utils'
</strong>import RLP from 'rlp'

const publicKeyToAddress = (publicKeyHex: string, networkType: 'mainnet' | 'testnet' = 'mainnet') => {
  const publicKeyBuffer = Buffer.from(publicKeyHex, 'hex')
  const network = networkType === 'mainnet' ? bitcoin.networks.bitcoin : bitcoin.networks.testnet

  // p2tr
  const toXOnly = (pubKey: Buffer) => (pubKey.length === 32 ? pubKey : pubKey.subarray(1, 33))
  const tapInternalKey = toXOnly(publicKeyBuffer)

  return {
    p2tr: bitcoin.payments.p2tr({ internalPubkey: tapInternalKey, network }).address!,
    p2pkh: bitcoin.payments.p2pkh({ pubkey: publicKeyBuffer, network }).address!,
    p2sh: bitcoin.payments.p2sh({
      redeem: bitcoin.payments.p2wpkh({ pubkey: publicKeyBuffer, network }),
    }).address!,
    p2wpkh: bitcoin.payments.p2wpkh({ pubkey: publicKeyBuffer, network }).address!,
  }
}
const toHexAddress = (address: string) => {
  const hash = keccak256(address)

  return '0x' + hash.slice(26)
}
const generateContractAddress = (walletAddress: string, nonce: number) => {
  const input = RLP.encode([walletAddress, nonce])
  const hash = keccak256(input)

  return '0x' + hash.slice(26)
}

// Example
const sampleInstruction: DeploySerialInstruction = {
  p: 'veda',
  publicKey: 'YOUR PUBLIC KEY HERE',
  addressType: 'p2wpkh',
  action: 'deploy',
  bytecodeLocation: 'BYTECODE ORDINALS ID HERE'
  nonce: 0,
  data: 'DATA HERE',
}
const ownerAddress = toHexAddress(
  publicKeyToAddress(sampleInstruction.publicKey)[sampleInstruction.addressType as 'p2pkh' | 'p2wpkh' | 'p2sh' | 'p2tr'],
)
const contractAddress = generateContractAddress(ownerAddress, sampleInstruction.nonce)
</code></pre>

```json
{
    "p": "veda",
    "publicKey": "YOUR PUBLICKEY HERE",
    "action": "execute",
    "contract": "CONTRACT ADDRESS",
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

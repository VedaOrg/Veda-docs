# Contract Address

Similar to wallet addresses, contract addresses in the EVM are also stored in a 20-byte hex format. Even if it doesn't need to be sliced by the Solidity address data type itself, address slicing issues can also arise during cross-contract calls. To maintain consistency with EVM behavior, the Veda application needs to directly input the pre-calculated Contract Address when constructing the execute instruction. The rule for calculating the Contract Address is to hash the deployer's hex address combined with the transaction nonce using **`keccak256`**.

## Example

```typescript
import * as bitcoin from 'bitcoinjs-lib'
import { keccak256 } from 'web3-utils'
import RLP from 'rlp'

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
```

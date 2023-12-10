# Instruction

## Instruction Define

```typescript
interface Instruction {
  p: 'veda'
  publicKey: string
  addressType: 'p2pkh' | 'p2sh' | 'p2wpkh' | 'p2tr'
  action: 'execute' | 'deploy'
  nonce: number
  data: string
  sigType: 'ecdsa' | 'bip-322'
}

interface ExecuteInstruction extends Instruction {
  contractLocation: string
}
interface DeployInstruction extends Instruction {
  bytecodeLocation: string
}

interface CompleteExecuteInstruction extends ExecuteInstruction {
  txHash: string
  sig: string
}
interface CompleteDeployInstruction extends DeployInstruction {
  txHash: string
  sig: string
}
```

## Instruction Properties

### p

This property is used for protocol matching. When the value of the **`p`** attribute is **`veda`**, it is considered a Veda instruction structure. After the veda-core successfully matches the protocol, it will begin checking the structure's properties and verifying the signature.

### publicKey

This property requires a **33-byte** compressed public key. Multisig wallets are not supported at the moment.

### addressType

This attribute requires setting your address type. Currently, **`p2pkh`**, **`p2sh`**, **`p2tr`** and **`p2wpkh`** formats are supported.&#x20;

### action

This attribute requires setting the instruction type. The available types are **`execute`** and **`deploy`**.

### nonce

This attribute is essentially the same as the **`nonce`** attribute in Ethereum. However, it's worth noting that in Ethereum, if a transaction with a nonce greater than the current transaction count is sent, that transaction will be held up until the transaction count matches the nonce. In Veda, due to the transaction order being based on ordinal numbers, **any nonce that doesn't match the current wallet transaction count will be discarded**.

### data

This attribute requires the encoded data of constructor arguments or the complete encoded data of a function call. Apart from deployments where its data would be relatively less compared to Ethereum, the mechanism is entirely consistent with Ethereum.

### sigType

This attribute requires the signature type. It supports **`ecdsa`** (recommended, used by Xverse wallet), and **`bip-322`** signature (used by Unisat wallet).&#x20;

It's **important** to note that the ECDSA signature is not signed in the standard ECDSA format, but rather in a format that complies with the [bitcoinjs-message](https://github.com/bitcoinjs/bitcoinjs-message) library's signature standard. This includes not only the **`RecoveryID`** in the V value, but also additional information about whether it's a **`compressed`** public key and the **`segwitType`** information.

### contract (Only available in execution instruction)

Calculate the contract's hex address based on the deployment instruction. Refer to [Generate Contract Address](address/contract-address.md#example)

### bytecodeLocation (Only available in deployment instruction)

This attribute requires the ordinals ID of the bytecode. For example, if I inscribed the bytecode of an ERC20 contract, and suppose the transaction hash of the inscription is abc with an offset of 0, then its ID would be **`abci0`**. Here, the character 'i' acts as a delimiter between the transaction hash and the offset. Next, to deploy the contract by invoking its constructor, I need to inscribe a deployment command. The **`bytecodeLocation`** attribute in this command will use the aforementioned **`abci0`**.

## CompleteInstruction Properties

### txHash

The transaction hash is obtained using keccak-256 hash after encoding the raw transaction data according to the Ethereum standard RLP. For specific examples, refer to[ Generate transaction hash](../how-to-use-veda/deploy-a-contract.md#generate-transaction-hash). The order of the RLP array is as follows:

```json
[
    "p",
    "publicKey",
    "addressType", // If addressType is undefined, replace with ""
    "action",
    "nonce",
    "data",
    "sigType" // If sigType is undefined, replace with ""
]
```

### sig

This attribute requires the signature result.

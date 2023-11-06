# Instruction

## Instruction Define

```typescript
interface Instruction {
  p: 'veda'
  publicKey: string
  addressType?: 'p2pkh' | 'p2sh' | 'p2wpkh'
  action: 'execute' | 'deploy'
  nonce: number
  data: string
  sigType?: 'compact' | 'der' | 'bip-322'
}

interface ExecuteInstruction extends Instruction {
  contractLocation: string
}
interface DeployInstruction extends Instruction {
  bytecodeLocation: string
}

interface CompleteExecueInstruction extends ExecuteInstruction {
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

### addressType (Nullable, default is p2wpkh)

This attribute requires setting your address type. Currently, **`p2pkh`**, **`p2sh`**, and **`p2wpkh`** formats are supported. Default value is **`p2wpkh`**. **`p2tr`** is temporarily not supported. For the specific reasons, please refer to [Wallet Address](address/wallet-address.md).

### action

This attribute requires setting the instruction type. The available types are **`execute`** and **`deploy`**.

### nonce

This attribute is essentially the same as the **`nonce`** attribute in Ethereum. However, it's worth noting that in Ethereum, if a transaction with a nonce greater than the current transaction count is sent, that transaction will be held up until the transaction count matches the nonce. In Veda, due to the transaction order being based on ordinal numbers, **any nonce that doesn't match the current wallet transaction count will be discarded**.

### data

This attribute requires the encoded data of constructor arguments or the complete encoded data of a function call. Apart from deployments where its data would be relatively less compared to Ethereum, the mechanism is entirely consistent with Ethereum.

### sigType(Nullable, default is compact)

This attribute requires the signature type. It supports **`compact`** (recommended, used by Unisat wallet), **`DER`** standard signature, and **`bip-322`** signature (used by Xverse wallet). The default value is **`compact`**.

### contractLocation (Only available in execution instruction)

This attribute requires the **ordinals id** of the contract, distinct from the **`bytecodeLocation`** attribute. The contract's ordinals id is the id of the contract deployment instruction on ordinals, while **`bytecodeLocation`** requires the id where the bytecode is inscribed on ordinals. For instance, if I inscribed the bytecode of an erc20 contract and suppose its inscription transaction hash is `abc` with an offset of 0, then its id would be `abci0`, **where the character 'i' acts as a delimiter between the transaction hash and the offset**. Next, to deploy the contract by invoking its constructor, I need to inscribe a deployment command. The **`bytecodeLocation`** attribute in this command would take the above-mentioned `abci0`. After the deployment command is inscribed, it results in a transaction hash, let's assume it's `edf` with an offset again being 0. Therefore, the id of the deployment command would be `edfi0`. Finally, to invoke a method in the contract, the **`contractLocation`** in the execution command should be `edfi0` and not `abci0`. You can view the content in `abci0` as the definition of a class, while `edfi0` is akin to instantiating the contract class through the constructor to deploy the contract.

### bytecodeLocation (Only available in deployment instruction)

Refer to [contractLocation](instruction.md#contractlocation-only-available-in-execution-instruction)

## CompleteInstruction Properties

### txHash

The transaction hash is obtained using keccak-256 hash after encoding the raw transaction data according to the Ethereum standard RLP. For specific examples, refer to[Generate transaction hash](../how-to-use-veda/deploy-a-contract.md#generate-transaction-hash). The order of the RLP array is as follows:

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

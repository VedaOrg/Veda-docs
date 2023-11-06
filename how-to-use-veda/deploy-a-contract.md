# Deploy a contract

## Deploy from an exists bytecode

### **Get your wallet public key**

* Unisat (Recommend)

```typescript
const publicKey = await window.unisat.getPublicKey()
```

* Xverse

```typescript
import { getAddress, GetAddressResponse, AddressPurpose, BitcoinNetworkType } from 'sats-connect'

const getAddress = () => {
  return new Promise((resolve, reject) => {
    const getAddressOptions = {
      payload: {
        purposes: [AddressPurpose.Payment],
        message: 'Address for signing veda message',
        network: {
          type: BitcoinNetworkType.Mainnet,
        },
      },
      onFinish: (response: GetAddressResponse) => {
        resolve(response.addresses[0])
      },
      onCancel: reject,
    }

    await getAddress(getAddressOptions)
  })
}
const publicKey = (await getAddress()).publicKey
```

### **Constructing deployment data**

#### Encode constructor arguments data

<pre class="language-typescript"><code class="lang-typescript">import axios from 'axios'
import Web3 from 'web3'

interface Contract {
  bytecode?: string
  abi?: string
}

<strong>const getInscriptionRequest = async &#x3C;T>(inscriptionId: string, path: string) => {
</strong>  return (await axios.get&#x3C;T>(`${ord}/${path}/${inscriptionId}`)).data
}
<strong>const getInscriptionContent = async &#x3C;T>(inscriptionId: string) => {
</strong>  return (await getInscriptionRequest&#x3C;T>(inscriptionId, 'content'))
}
const getConstructorAbiItem = (abi: string) => {
  return JSON.parse(abi).find((item: any) => item.type === 'constructor') || null;
}

const web3 = new Web3()
const args = [
  // Your constructor arguments here
]
<strong>const { bytecode, abi } = await getInscriptionContent&#x3C;Contract>(contract)
</strong>const data = web3.eth.abi.encodeParameters(getConstructorAbiItem(abi).inputs, args) // args is constructor arguments.
</code></pre>

You can also obtain the ABI file and encoded data in any way you prefer. The above is just a simple demo to show you how to encode the data required by Veda.

```json
{
    "p": "veda",
    "publicKey": "YOUR PUBLICKEY HERE",
    "action": "deploy",
    "bytecodeLocation": "EXAMPLE BYTECODE INSCRIPTION ID",
    "nonce": 0, // Wallet tx count
    "data": "ENCODE ARGUMENGTS DATA HERE",
}
```

The deployment method of Veda differs from that of ETH. Since ETH cannot persistently store the complete contract bytecode on its own, it needs to concatenate the bytecode and the encoded constructor parameters as the complete data in a transaction. However, in Veda, to facilitate the reuse of bytecode, only the encoded constructor parameters are required when passing data. The complete bytecode data is directly fetched by Veda-core via Ordinals. This not only saves the size of the 'deploy' instruction but also makes it convenient to reuse contract bytecode.

### Generate transaction hash

```typescript
import { rlp, intToBuffer } from 'ethereumjs-util'
import { keccak256 } from 'web3-utils'

interface DeployInstruction {
  p: 'veda'
  publicKey: string
  addressType?: 'p2pkh' | 'p2sh' | 'p2wpkh'
  action: 'execute' | 'deploy'
  bytecodeLocation: string
  nonce: number
  data: string
  sigType?: 'compact' | 'der' | 'bip-322'
}

const serializeInstruction = (instruction: DeployInstruction) => {
  const instructionAsArray = [
    instruction.p,
    instruction.publicKey,
    instruction.addressType || '',
    instruction.action,
    ethUtil.intToBuffer(instruction.nonce),
    instruction.data,
    instruction.sigType || ''
  ]
  
  return ethUtil.rlp.encode(instructionAsArray)
}

const sampleInstruction: DeployInstruction = {
  p: 'veda',
  publicKey: 'yourPublicKey',
  action: 'deploy',
  bytecodeLocation: 'bytecodeId'
  nonce: 0,
  data: 'yourData',
}

const serializedData = serializeInstruction(sampleInstruction)
const txHash = keccak256(serializedData)

```

### **Sign deployment data**

* Unisat (Recommend, SigType: compact)

```typescript
const txHash: string // generated
const sig = await window.unisat.signMessage(txHash)
```

* Xverse (SigType: bip-322)

```typescript
const txHash: string // generated
const address = (await getAddress()).address
const getSig = () => {
  return new Promise((resolve, reject) => {
    const signMessageOptions = {
      payload: {
        network: {
          type: BitcoinNetworkType.Mainnet,
        },
        address,
        message: txHash,
      },
      onFinish: resolve,
      onCancel: reject,
    }
  })
}

const sig = await getSig()
```

### **Re-Constructing instruction**

```
{
    "p": "veda",
    "txHash": "YOUR TRANSACTION HASH HERE",
    "publicKey": "YOUR PUBLICKEY HERE",
    "action": "deploy",
    "bytecodeLocation": "EXAMPLE BYTECODE INSCRIPTION ID",
    "nonce": 0, // Wallet tx count
    "data": "ENCODE ARGUMENGTS DATA HERE",
    "sig": "SIGNATURE HERE"
}
```

### **Inscribe deployment data**

You can use any way to inscribe your data. Veda org will also deploy an inscribe service in few weeks.

## Deploy your own bytecode

### **Compile your contract**

You can compile your contract on [https://remix.ethereum.org](https://remix.ethereum.org/) or use any other method you prefer.

### **Constructing bytecode and abi**

```json
{
    "bytecode": "YOUR CONTRACT BYTECODE HERE",
    "abi": "YOUR ABI STRING HERE", // Must stringify your abi
}
```

### Inscribe bytecode data

See [Inscribe deployment data](deploy-a-contract.md#inscribe-deployment-data)

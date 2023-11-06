# Contract Address

Similar to wallet addresses, contract addresses in the EVM are also stored in a 20-byte hex format. Even if it doesn't need to be sliced by the Solidity address data type itself, address slicing issues can also arise during cross-contract calls. Unlike Ethereum, when Veda creates a contract, it doesn't generate a hash using the deployer's address plus nonce. Instead, it directly hashes the deployment transaction's ID on the ordinals to obtain the contract address. This design primarily considers completing this step with fewer ordinals requests. At the same time, when making a bvm eth\_call, the requested contract address also needs to use a 20-byte hex address type. This way, without making additional HTTP requests, you can directly use the contract's deployment ID to generate a 20-byte contract address, enhancing the application's usability.

## Example

```typescript
import { keccak256 } from 'web3-utils'

const contractDeployId = 'YOUR CONTRACT DEPLOY ID HERE'
const hash = keccak256(contractDeployId)
const contractAddress = '0x' + hash.slice(-40)
```

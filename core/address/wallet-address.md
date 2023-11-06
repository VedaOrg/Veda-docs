# Wallet Address

## Bitcoin address and Ethereum address

We know that even if the EVM allows for addresses of any length, the **`Address`** type in Solidity will still **enforce storing only the last 20 bytes**. Since this behavior occurs within the Solidity compiler, if we want to fully store a Bitcoin address, it would require modifications to the Solidity compiler. This would add a layer of complexity to Veda's development and also hinder the interoperability between Veda and Ethereum's ecosystem. Therefore, we decode BTC addresses from base58check or bech32, resulting in a valid 20-byte data. Converting this 20-byte data into hexadecimal format gives us an address that fully complies with Solidity's standards. This process is also reversible; the 20-byte data can be re-encoded to retrieve the original BTC address. The above behavior applies to **`p2pkh`**, **`p2sh`**, and **`p2wpkh`** addresses.

### How about P2TR?

P2TR uses `bech32m` encoding, and when decoded, it produces a **33-byte** Schnorr public key. This is different from the aforementioned decoding process that results in a 20-byte public key hash. Converting a 33-byte public key to a 20-byte Ethereum address will inevitably result in a loss of information, rendering the process irreversible. Given the current uncertainty about how this irreversibility might impact Veda's future implementations, P2TR address formats are not currently supported.

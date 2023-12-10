# Wallet Address

## Bitcoin address and Ethereum address

We know that even if the EVM allows for addresses of any length, the **`Address`** type in Solidity will still **enforce storing only the last 20 bytes**. Since this behavior occurs within the Solidity compiler, if we want to fully store a Bitcoin address, it would require modifications to the Solidity compiler. This would add a layer of complexity to Veda's development and also hinder the interoperability between Veda and Ethereum's ecosystem. We directly hash the BTC address using **`keccak256`** and take the 20-byte data, which results in a standard compliant hex address.

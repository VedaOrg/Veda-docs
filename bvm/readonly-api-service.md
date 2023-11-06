# Readonly API Service

BVM will provide a JSON RPC service, which will fully comply with the **`eth_call`** method standard, allowing developers and users to query the contract's internal state and simulate transactions through this interface.

## Address Type Conversion During Query

When the BVM stores data of the address type, it only stores 20-byte long hex addresses, and the BVM does not handle any form of BTC address conversion. Therefore, when querying, developers need to pre-convert the BTC address to a 20-byte long hex address before performing the query. This also applies to the query of contract addresses. The Veda-SDK will provide utility functions to assist developers in address conversion.

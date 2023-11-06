# Glossary or Definitions

## Ordinals ID

Ordinals ID is a unique identifier generated during ordinals engraving. It consists of two parts: the engraving transaction hash and the output offset, separated by the character 'i'. Here's an example of an id: `e3a21b5c12345678f90abcde1234567890abcdef0i0`.

## Deploy ID

Deploy ID refers to the ordinals id generated when the contract deploy instruction is engraved on ordinals. This id should be used as the contract address in actual use. During engraving, it can be filled into the veda instruction in the form of the original id. However, during eth\_call queries, the veda-sdk method needs to be called or manually perform a keccak256 hash to take the last 20 bytes hex for address conversion.

# What will Erde bring?

## A high-efficiency L2 that can be directly integrated with BTC wallets for use

Unlike the abstract account usage that relies on centralized third parties available in the market, Erde will support the use of BTC wallets with EVM L2 directly by modifying the internal transaction structure and signing methods. Decentralized wallet access, compared to centralized abstract accounts, will bring higher usability while eliminating concerns about economic losses due to private keys being held by third parties. Moreover, because the L2 chain transaction format will include the BTC address, it is possible to conduct airdrops or other operations for users on the BTC L1 by pulling L2 data, achieving an ecosystem giveback.

## Miners' stable earnings

Based on the Optimism Rollup design, treating BTC L1 as the Data Availability (DA) layer and utilizing BTC L1 Veda as the verification layer will provide each BTC L1 block with a stable transaction data size. Unlike the ETH Block gas limit restriction, the limitation for BTC Block is the block size. Observing BTC historical data, most of the time, miners' fee income is minimal due to insufficient transaction data volume within blocks. The miners' fee income depends not only on the feeRate premium but more importantly, on having a sufficient volume of transaction data in each block. This means that even if Erde submits only one or two Rollup verification transactions to BTC per block, it can still provide a stable income for miners based on the sufficient data size of each transaction.

## The PoS staking and re-staking of Veda L1 assets

Veda's perseverance to this day could not have been achieved without the support of the community. Likewise, the community-governed asset $VEDA on Veda will also be supported by Erde. Since Erde sequencers are required to stake assets on Veda to prevent malicious activities by L2 nodes, we have decided to use $VEDA as the staking asset for Erde sequencers. Furthermore, we will launch Erde staking to support users in staking and obtaining an equivalent amount of $ERDE on L2. Similar to $stEth and $ETH, after $ERDE is unstaked on L2, in addition to receiving an equivalent amount of $VEDA, users can also claim their earned rewards. To facilitate unchanged earnings calculations for holders during re-staking, $wERDE (equivalent to $wstETH) will be introduced to ensure users continue to enjoy staking rewards during re-staking. Erde's superchain will use $BTC as its native token, and all PoS earnings distributions will be made in the form of $BTC.

## Erde Stack, community governance on Layer 2, and interoperability among different Layer 2 solutions

### Erde Stack

Erde Stack is a set of software designed to support Erde. Initially, it will appear in the form of software behind the Erde mainnet, ultimately manifesting in the form of Erde Superchain and its governance.

With the emergence of the Superchain concept, it becomes increasingly crucial for Erde to facilitate the secure creation of new chains that can seamlessly interact within the proposed Superchain ecosystem. Therefore, Erde Stack primarily focuses on establishing a shared, high-quality, fully open-source system for creating new L2 blockchains. By coordinating shared standards, the optimistic collective can avoid redundant rebuilding of the same software in isolation.

**Erde Stack can be seen as software components that help define specific layers of the Erde ecosystem and can also serve as modules within existing layers.** While the current core of Erde Stack revolves around running L2 blockchains infrastructure, theoretically, it can extend to layers above the underlying blockchain, including tools such as block explorers, messaging mechanisms, governance systems, and more.

### Community governance on Layer 2 and Layer 2 interoperability

The BTC L1 ecosystem differs from other ecosystems in that it focuses more on the development of protocols for various types of assets. While these assets can all rely on the UTXO model for trustless transactions, the lack of compatibility among protocol data prevents different assets from sharing infrastructure and interoperability. Additionally, most L1 protocols still utilize off-chain indexing similar to the brc-20, with the indexing code merge and operational rights resting with protocol developers, making it difficult for community developers to add more gameplay to the protocol. Therefore, from a developmental perspective, different protocols/communities need their own L2 to establish more gameplay for assets. However, not all teams can build an L2 from scratch. This is where Erde Stack comes into play. It can quickly help teams establish an L2 based on BTC L1 and Veda, allowing community developers to update protocol assets without permission.

Even when L2 is established, infrastructure on L2 is relatively lacking in the early stages. At this point, interoperability between different L2 solutions becomes particularly important. Imagine being able to leverage various existing DeFi facilities on the Erde Superchain for basic transaction needs once L2 is established. Teams and community developers can then shift their focus to developing gameplay for protocol assets themselves, without having to spend a significant amount of time and effort reinventing the wheel on L2. Similarly, interoperability opens up better liquidity support for assets because there is no need to cross-chain assets between different L2 solutions.

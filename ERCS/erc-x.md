---
title: PermaLink Asset Bound Token Standard
description: An interface for PermaLink Asset Bound Tokens, also known as a PermaLink-ABTs.
author: Mihai Onila (@MihaiORO), Nick Zeman (@NickZCZ), Narcis Cotaie (@NarcisCRO)
discussions-to: https://ethereum-magicians.org/t/non-fungible-asset-bound-token/23175
status: Draft
type: Standards Track
category: ERC
created: 2025-04-01
requires: <EIP number(s)> # Only required when you reference an EIP in the `Specification` section. Otherwise, remove this field.
---

### Abstract

This standard introduces a subclass of tokens known as **PermaLink Asset Bound Tokens (PermaLink-ABTs)**—a specific implementation of the broader **Asset Bound Token (ABT)** concept. ABTs establish a novel ownership paradigm where **an asset can own another asset**, enabling composable, nested, and portfolio-like token structures that evolve together over time.

PermaLink-ABTs implement a permanent binding mechanism where a token in one smart contract is irreversibly linked to a token in another contract. These links mirror key state data—such as `ownerOf`, `tokenId`, `totalSupply`, and `balanceOf`—using the `assetBoundContract` interface. Traditional token transfer and approval functions are disabled to enforce immutability and structural cohesion between bound assets.

Instead of utilizing a `mint` function, PermaLink-ABTs employ a `reveal` mechanism that activates tokens from a predefined supply. This approach enables permissionless binding and significantly reduces gas costs. A single token can have multiple PermaLink-ABTs bound to it acting as multiple multiple subordinate assets, forming a unified, transferable unit that simplifies asset mobility across digital identities, NFTs, and real-world assets (RWAs).

By encouraging asset composability over competition, PermaLink-ABTs introduce a dynamic, future-proof model for on-chain asset evolution.


## Motivation

Currently, there is a limitation to traditional ownership models as only wallets or addresses can own blockchain assets. As digital identities, real-world assets (RWAs), and digital collectibles continue to grow, one common denominator emerges: smart contracts. While these contracts serve as the foundation for on-chain innovation, they are inherently static. Once deployed, they cannot be modified to accommodate evolving ideas, unforeseen use cases, or new integrations.

During the development process, multiple use cases where a new ownership model would be beneficial were established;

1. **The Case for On-Chain Identity:** Digital identity issuance is already taking shape worldwide, with nations like China, India, and Singapore leading the way. The EU, US, and others are also exploring digital passports, state IDs, and blockchain-based social security numbers. These identity frameworks require seamless linking to healthcare, driver's licenses, bank accounts, and voting registries.
If each ID or registry exists as an isolated smart contract, users must manually track and transfer numerous tokens and credentials—a process prone to human error. A universal on-chain identity should enable assets to be bound together, allowing them to move as a unit rather than requiring manual migration. Asset Bound Tokens (ABTs) provide a framework for this, ensuring that identity-linked assets remain interconnected and dynamic.

2. **Real-World Assets and Ownership Structure**: RWAs are gaining traction, from tokenized commodities like gold (e.g., BRICS-backed gold currency) to entire corporations and their underlying assets. Unlike static collectibles, companies actively buy, sell, and manage assets. A farming company acquiring new land or upgrading machinery, or an IT firm merging with another and inheriting IP, necessitates an ownership structure that reflects these changes.
Currently, assets locked within smart contracts cannot be seamlessly transferred across contracts. ABTs address this by enabling hierarchical ownership structures where assets within a contract can be linked and updated as ownership evolves. This ensures businesses can efficiently manage assets on-chain without the constraints of rigid smart contracts.

3. **Manufacturing and Inventory Management:** Manufacturing supply chains involve complex hierarchies: products are packed into boxes, which are placed on pallets, which are stored in containers. Each step requires precise tracking, from raw materials to final products reaching consumers. Immutable records on blockchain offer transparency, but the current model—creating individual smart contracts or repeatedly minting new tokens—is costly and inefficient.
ABTs streamline inventory management by allowing dynamic asset binding. Instead of creating redundant smart contracts, manufacturers can link tokens representing various stages of the supply chain, maintaining historical data while ensuring seamless tracking and updates.

4. **Addressing NFT Fragmentation:** NFTs have traditionally been associated with digital collectibles and art. However, many projects deploy secondary smart contracts to evolve their collections, inadvertently causing liquidity fragmentation. Owners often sell assets from the original collection to acquire newer ones, leading to value dilution and lower overall market confidence.
ABTs solve this by allowing secondary collections to complement rather than compete with the original. Bound assets enhance the primary asset’s value without necessitating a separate, competing ecosystem. This structure retains liquidity within the original collection and sustains its market metrics, benefiting both creators and collectors.

5. **New Opprutunites for Creators:** ABTs empower both asset owners and creators by enabling an open secondary market for existing tokens. Artists, for instance, can create and bind assets to existing NFTs without requiring permission from the original contract owner. This facilitates new revenue streams, such as artists being paid on consignment for augmenting or adding onto existing assets. Owners benefit as well, as additional assets increase the inherent value of their holdings, especially in instances of collaboration between established projects or reputable artists.

In general, the concept of ABTs establishes a token standard where one token is bound to another by linking rather than direct ownership. If the binding token moves, all bound assets update accordingly, preserving structure without requiring manual transfers. This approach transforms smart contracts into dynamic, evolving repositories, ensuring long-term viability for digital identities, RWAs, and NFTs alike.

Whether enhancing identity systems, optimizing supply chains, or fostering NFT innovation, ABTs introduce a flexible, future-proof ownership model that aligns with the ever-evolving nature of blockchain ecosystems. In the case of **PermaLink-ABTs**, these specifically focus on a permanent link between the ABT either another ABT or an NFT. This results in the supposed increase in the value of the binding token due to the addition of a new asset bound to it. This ABT version also helps if a user would like to move a whole portfolio of ABTs at once, reducing gas fees, as only the binding token has to be moved in order for all of the PermaLink ABTs to move with it.







-->

## Specification

<!--
  The Specification section should describe the syntax and semantics of any new feature. The specification should be detailed enough to allow competing, interoperable implementations for any of the current Ethereum platforms (besu, erigon, ethereumjs, go-ethereum, nethermind, or others).

  It is recommended to follow RFC 2119 and RFC 8170. Do not remove the key word definitions if RFC 2119 and RFC 8170 are followed.

  TODO: Remove this comment before submitting
-->

The key words "MUST", "MUST NOT", "REQUIRED", "SHALL", "SHALL NOT", "SHOULD", "SHOULD NOT", "RECOMMENDED", "NOT RECOMMENDED", "MAY", and "OPTIONAL" in this document are to be interpreted as described in RFC 2119 and RFC 8174.

## Rationale

<!--
  The rationale fleshes out the specification by describing what motivated the design and why particular design decisions were made. It should describe alternate designs that were considered and related work, e.g. how the feature is supported in other languages.

  The current placeholder is acceptable for a draft.

  TODO: Remove this comment before submitting
-->

TBD

## Backwards Compatibility

<!--

  This section is optional.

  All EIPs that introduce backwards incompatibilities must include a section describing these incompatibilities and their severity. The EIP must explain how the author proposes to deal with these incompatibilities. EIP submissions without a sufficient backwards compatibility treatise may be rejected outright.

  The current placeholder is acceptable for a draft.

  TODO: Remove this comment before submitting
-->

No backward compatibility issues found.

## Test Cases

<!--
  This section is optional for non-Core EIPs.

  The Test Cases section should include expected input/output pairs, but may include a succinct set of executable tests. It should not include project build files. No new requirements may be introduced here (meaning an implementation following only the Specification section should pass all tests here.)
  If the test suite is too large to reasonably be included inline, then consider adding it as one or more files in `../assets/eip-####/`. External links will not be allowed

  TODO: Remove this comment before submitting
-->

## Reference Implementation

<!--
  This section is optional.

  The Reference Implementation section should include a minimal implementation that assists in understanding or implementing this specification. It should not include project build files. The reference implementation is not a replacement for the Specification section, and the proposal should still be understandable without it.
  If the reference implementation is too large to reasonably be included inline, then consider adding it as one or more files in `../assets/eip-####/`. External links will not be allowed.

  TODO: Remove this comment before submitting
-->

## Security Considerations

PermaLink-ABTs are linked to another non-fungible token. If an individual loses access to this token, what we call the binding token, they also lose access to all PermaLink-ABTs that have been bound to it. This is why we strongly recommend utilizing a standard such ERC-6809, a Non-Fungible Key Bound Token, because this token standard provides on-chain 2FA. This would secure all of the PermaLink-ABTs bound to the ERC-6809, and also allow a way to retrieve all of the tokens in case access to the wallet is lost or you’ve connected to a malicious site. In essence, as all of ERC-6809s security functionality carry of to all of the PermaLink-ABTs bound to it.

Needs discussion.

## Copyright

Copyright and related rights waived via [CC0](../LICENSE.md).

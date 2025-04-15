---
title: PermaLink Asset Bound Token Standard
description: An interface for PermaLink Asset Bound Tokens, also known as a PermaLink-ABTs.
author: Mihai Onila (@MihaiORO), Nick Zeman (@NickZCZ), Narcis Cotaie (@NarcisCRO)
discussions-to: https://ethereum-magicians.org/t/non-fungible-asset-bound-token/23175
status: Draft
type: Standards Track
category: ERC
created: 2025-04-01
requires: 721, 721Enumerable
---

### Abstract

This standard introduces a subclass of tokens known as **PermaLink Asset Bound Tokens (PermaLink-ABTs)** a specific implementation of the broader **Asset Bound Token (ABT)** concept. ABTs establish a novel ownership paradigm where **an asset can own another asset**, enabling composable, nested, and portfolio-like token structures that evolve together over time.

PermaLink-ABTs implement a permanent binding mechanism where a token in one smart contract is irreversibly linked to a token in another contract. These links mirror key state data such as `ownerOf`, `tokenId`, `totalSupply`, and `balanceOf` using the `assetBoundContract` interface. Traditional token transfer and approval functions are disabled to enforce immutability and structural cohesion between bound assets.

Instead of utilizing a `mint` function, PermaLink-ABTs employ a `reveal` mechanism that activates tokens from a predefined supply. This approach enables permissionless binding and significantly reduces gas costs. A single token can have multiple PermaLink-ABTs bound to it acting as multiple multiple subordinate assets, forming a unified, transferable unit that simplifies asset mobility across digital identities, NFTs, and real-world assets (RWAs).

By encouraging asset composability over competition, PermaLink-ABTs introduce a dynamic, future-proof model for on-chain asset evolution.


## Motivation

Traditional ownership models on Ethereum are inherently limited—only externally owned accounts (EOAs) or smart contracts can own blockchain assets. This creates rigidity, especially as the blockchain ecosystem expands to include digital identities, tokenized real-world assets (RWAs), and NFTs. Current smart contracts are static in nature, meaning once deployed, they cannot adapt to evolving systems, unforeseen use cases, or new integration layers. There is a need for a more flexible and modular ownership model that allows for dynamic interactions between assets.

This standard proposes **Asset Bound Tokens (ABTs)** as a solution to this challenge. ABTs allow one token to be permanently bound to another across contracts, creating a dynamic, flexible, and composable ownership model. By enabling tokens to move and evolve together, ABTs pave the way for new possibilities in the on-chain economy. The following use cases illustrate why ABTs are needed:

1. **On-Chain Identity Systems**  
 Governments and institutions worldwide are piloting or implementing blockchain-based identity systems—digital passports, national IDs, and verifiable credentials such as with the EBSI initiative. These systems often require credentials to be linked across multiple registries (e.g., healthcare, banking, voting). ABTs enable the binding of identity-linked tokens into a cohesive unit, so they move together instead of requiring manual coordination and transfers. This ensures that identity-linked assets remain interconnected and dynamic, making it easier to manage and update linked data as users interact with various systems.

2. **Real-World Asset (RWA) Ownership Structures**  
 Tokenized businesses and assets (e.g., land, equipment, commodities) need flexible ownership models. These dynamic—businesses acquire, divest, and restructure their holdings and various assets. ABTs allow contracts to represent complex, evolving ownership hierarchies, where nested assets follow changes in their parent entity’s structure (e.g., a farming company acquiring new land or an IT firm merging with another and inheriting intellectual property). ABTs ensure businesses can efficiently manage and transfer assets on-chain without the constraints of rigid smart contracts.

3. **Manufacturing and Supply Chain Management**  
    Supply chains involve multiple layers of assets: raw materials → parts → products → packaging → containers. Blockchain’s transparency is invaluable, but traditional methods of creating individual tokens or smart contracts for each stage are inefficient and costly. ABTs streamline this by linking tokens across the supply chain, allowing them to be aggregated when products are built up (e.g., shoes packed in boxes, boxes placed on pallets, pallets loaded into containers) and broken down as they move through different stages (e.g., containers unloaded, pallets split, boxes unpacked). This dynamic linking and unlinking reduce redundancy, maintain transparent immutable records, and ensure seamless tracking while minimizing gas costs. By enabling the efficient flow of assets throughout the supply chain, ABTs help reduce complexity and provide a cohesive, real-time view of the entire process.

4. **NFT Ecosystem Optimization**  
 NFT projects often expand by launching secondary collections (e.g., additional editions, special releases). Without ABTs, this leads to fragmented value and user confusion as older and newer assets compete. ABTs allow new NFTs to be bound to originals, enhancing their value while maintaining a unified ecosystem. This strengthens liquidity and preserves market metrics, ensuring that the value of the original collection is retained and supported by the newer assets, thus benefiting both creators and collectors.

5. **New Opportunities for Creators**  
   ABTs empower creators to build on top of existing assets permissionlessly—without needing ownership of or permission from the original smart contract. This enables a new wave of creative expression, where artists can augment and enhance NFTs (e.g., adding new visuals, audio, or interactive layers) and collaborate on existing collections. Such contributions can generate new revenue streams through shared royalties, consignment, or collaborative upgrades. Owners benefit as well, since bound enhancements can increase the inherent value of their holdings, particularly in projects involving established creators or cross-collection collaborations.

In essence, ABTs introduce a framework where tokens are linked rather than owned. This allows for dynamic and evolving asset systems, where assets move together in harmony. If a binding token moves, all associated ABTs move with it, ensuring seamless updates and reducing the need for manual transfers. This innovation transforms traditional smart contracts from static repositories into living, evolving systems capable of adapting to changing use cases, technologies, and business models.

The **PermaLink-ABTs** implementation takes the ABT model further by enforcing a permanent binding between one token and another—whether it’s another ABT, NFT or NFKBT. This permanent binding ensures that tokens can be transferred as a single unit, reducing complexity and gas fees. Instead of relying on traditional minting, PermaLink-ABTs use a `reveal` mechanism, activating tokens from a predefined supply. This reduces gas costs and encourages efficient linking of assets, enabling greater composability across multiple sectors.

PermaLink-ABTs consolidate asset value by allowing multiple subordinate tokens to be linked to a single binding token, providing enhanced composability and reducing fragmentation. By requiring only the binding token to be transferred, all associated assets move in sync, making it easier to manage portfolios and move groups of assets together. This approach fosters collaboration, value accrual, and compatibility across ecosystems, whether for digital identities, RWAs, NFTs, or other on-chain assets.


## Specification

### `IABT` (Token Interface)

**NOTES**:

- The following specifications use syntax from Solidity `0.8.27` (or above)
- Callers MUST handle `false` from `returns (bool success)`. Callers MUST NOT assume that `false` is never returned!

```solidity
interface IABT {
    function ownerOf(uint256 tokenId) external view returns (address);
    function tokenExists(uint256 tokenId) external view returns (bool);
}
```

### `ABT` (Token Contract)
```solidity
contract ABT is ERC721Enumerable, Ownable, IABT {
    ERC721Enumerable public assetBoundContract;

    constructor(
        address _assetBoundContract,
        string memory _name,
        string memory _symbol
    ) ERC721(_name, _symbol) {
        assetBoundContract = ERC721Enumerable(_assetBoundContract);
    }

    function ownerOf(
        uint256 tokenId
    ) public view override(ERC721, IABT, IERC721) returns (address) {
        return assetBoundContract.ownerOf(tokenId);
    }

    function tokenExists(uint256 tokenId) public view returns (bool) {
        return assetBoundContract.ownerOf(tokenId) != address(0);
    }

    function totalSupply() public view override returns (uint256) {
        return assetBoundContract.totalSupply();
    }

    function balanceOf(
        address owner
    ) public view override(ERC721, IERC721) returns (uint256) {
        return assetBoundContract.balanceOf(owner);
    }

    function approve(address, uint256) public pure override(ERC721, IERC721) {
        revert("ABT: Approvals not allowed");
    }

    function setApprovalForAll(
        address,
        bool
    ) public pure override(ERC721, IERC721) {
        revert("ABT: Approvals not allowed");
    }

    function transferFrom(
        address,
        address,
        uint256
    ) public pure override(ERC721, IERC721) {
        revert("ABT: Transfers not allowed");
    }

    function safeTransferFrom(
        address,
        address,
        uint256
    ) public pure override(ERC721, IERC721) {
        revert("ABT: Transfers not allowed");
    }

    function safeTransferFrom(
        address,
        address,
        uint256,
        bytes memory
    ) public pure override(ERC721, IERC721) {
        revert("ABT: Transfers not allowed");
    }
}
```

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

PermaLink-ABTs are linked to another non-fungible token. If an individual loses access to this token—what we call the **binding token**—they also lose access to all PermaLink-ABTs that have been bound to it. This introduces a critical security consideration: the entire value of bound assets depends on the integrity and availability of the binding token.

To mitigate this risk, we strongly recommend the use of standards like [ERC-6809](https://eips.ethereum.org/EIPS/eip-6809), a **Non-Fungible Key Bound Token**, which introduces on-chain two-factor authentication (2FA). ERC-6809 allows a user to bind sensitive tokens (like PermaLink-ABTs) to a secured identity layer, complete with recovery mechanisms. In the event that a user loses access to their original wallet or interacts with a malicious contract, ERC-6809 provides a safeFallback function to re-establish control.

In essence, all of the security guarantees of ERC-6809 extend to any PermaLink-ABTs bound to it. This layered security model not only protects against loss but also ensures recoverability and long-term viability for high-value bound assets. It is strongly encouraged that developers implementing PermaLink-ABTs integrate this or similar standards to provide a robust security foundation for users.



Needs discussion.

## Copyright

Copyright and related rights waived via [CC0](../LICENSE.md).

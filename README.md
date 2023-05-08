# BRC-721: Non-Fungible Tokens on the Bitcoin Network

BRC-721 is inspired by the BRC-20, an experimental standard for creating fungible tokens on the Bitcoin network. BRC-20, which can be found at [brc-20-experiment](https://domo-2.gitbook.io/brc-20-experiment), aims to bring the functionality of Ethereum's ERC-20 tokens to the Bitcoin ecosystem. By building upon the ideas and principles of BRC-20, BRC-721 extends the capabilities of tokenization on the Bitcoin network to include non-fungible tokens, thus enabling a broader range of digital asset management and value representation.

BRC-721 is designed for non-fungible tokens (NFTs) on the Bitcoin network. It allows for the creation, ownership, and transfer of unique digital assets on the Bitcoin blockchain. Each token created under BRC-721 has a unique identifier, making them distinct and non-interchangeable.  

## Operations

### Deploy brc-721

``` json
{
    "p": "brc-721",
    "op": "deploy",
    "tick": "ordinals",
    "buri": "https://ipfs.io/",
    "max": "10000",
    "lim": "1"
}
```

| Key | Required? | Description |
|---|---|---|
| p | Yes | Protocol: Helps other systems identify and process brc-721 events |
| op | Yes | Operation: Type of event (deploy, mint, transfer, update) |
| tick | Yes | Ticker: identifier of the brc-721, no more than 16 letters |
| buri | Yes | BaseURI URI for the brc-721, access `{buri}{token_id}` for the token information |
| max | Yes | Max supply: set max supply of the brc-721 |
| lim | No | Mint limit: if letting users mint to themselves, limit per ordinal |

For more information about tokenURI and metadata, please refer to [EIP-721](https://eips.ethereum.org/EIPS/eip-721) and [metadata standards](https://docs.opensea.io/docs/metadata-standards).

### Mint brc-721

``` json
{
    "p": "brc-721",
    "op": "mint",
    "tick": "ordinals",
    "amt": "1"
}
```

| Key | Required? | Description |
|---|---|---|
| p | Yes | Protocol: Helps other systems identify and process brc-721 events |
| op | Yes | Operation: Type of event (deploy, mint, transfer, update) |
| tick | Yes | Ticker: identifier of the brc-721, no more than 16 letters |
| amt | No | Amount to mint: States the amount of the brc-721 to mint. Has to be less than "lim" above if stated, default amt is 1 |

> token id is generated from 1 to "max" according to the inscription id  

### Transfer brc-721

Transfer one token:  

``` json
{
    "p": "brc-721",
    "op": "transfer",
    "tick": "ordinals",
    "id": "1"
}
```

Transfer multiple tokens:

``` json
{
    "p": "brc-721",
    "op": "transfer",
    "tick": "ordinals",
    "ids": ["1", "2"]
}
```

| Key | Required? | Description |
|---|---|---|
| p | Yes | Protocol: Helps other systems identify and process brc-721 events |
| op | Yes | Operation: Type of event (deploy, mint, transfer, update) |
| tick | Yes | Ticker: identifier of the brc-721, no more than 16 letters |
| id | No | Token id to transfer, either "id" or "ids" should be stated |
| ids | No | Multiple token ids to transfer, either "id" or "ids" should be stated |

### Update Metadata

``` json
{
    "p": "brc-721",
    "op": "update",
    "tick": "ordinals",
    "buri": "https://ipfs.io/"
}
```

| Key | Required? | Description |
|---|---|---|
| p | Yes | Protocol: Helps other systems identify and process brc-721 events |
| op | Yes | Operation: Type of event (deploy, mint, transfer, update) |
| tick | Yes | Ticker: identifier of the brc-721, no more than 16 letters |
| buri | Yes | BaseURI URI for the brc-721, access {buri}{token_id} for the token information |

> This operation should be only allowed for the deployer.

## FAQ

### What is the difference between brc-721 and ordinals inscription?

Similar to brc-20, brc-721 is built upon the ordinals protocol. Although ordinals inscription can store images by itself, there are significant functional differences between brc-721 and ordinals:

* Data storage

    * ordinals stores image data in the witness field, which has the advantage of storing data on-chain without relying on third parties. This is useful for applications that prioritize asset security. However, it also introduces some issues, such as high minting costs and occupying a large amount of Bitcoin network space.
    * Brc-721 saves images and other data in external services, greatly reducing the space occupied on the Bitcoin network.

* Compatibility with brc protocol

    * brc-721 adopts a similar protocol format to brc-20, using JSON to define different functions, which greatly enhances the flexibility of NFTs. For example, it supports the reveal operation by updating the base URI; the tick field enables efficient indexing of NFTs in a collection.

* Compatibility with the NFT ecosystem

    * The erc-721 standard NFT is more popular in the current market. Brc-721's token URI and metadata specifications are consistent with erc-721, allowing for quick adaptation to the existing NFT ecosystem. In addition, while ordinals does not support fields such as traits, brc-721 supports defining NFT attributes and rarity information.

### What is the difference between brc-721 and brc721.com?

brc721.com also proposes a non-fungible token (NFT) solution based on the ordinals protocol, but its protocol is more complex and not compatible with brc-20. In contrast, brc-721 aims to maintain consistency with the brc-20 standard.

## Contribution

BRC-721 is an experimental standard that brings non-fungible tokens (NFTs) to the Bitcoin network. With this standard, users can create, mint, transfer, and update unique digital assets, enabling a wide range of use cases, such as digital art, collectibles, virtual goods, and more.  

The standard allows for a series of operations that facilitate the management of non-fungible tokens, including deployment, minting, transferring, and updating metadata. Each token is assigned a unique identifier, ensuring that each NFT is distinct and cannot be exchanged on a one-to-one basis with another NFT.  

As an experimental standard, BRC-721 invites improvements and modifications to enhance its functionality and adapt it to the growing needs of the NFT ecosystem.

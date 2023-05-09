[English](./README.md) | [中文](./README_zh.md)

---

# BRC-721：比特币网络上的非同质化代币

BRC-721的灵感来源于BRC-20，这是一个在比特币网络上创建可替代代币的实验性标准。BRC-20的详细信息可以在[brc-20-experiment](https://domo-2.gitbook.io/brc-20-experiment/)中找到，其目标是将发行同质化代币的功能引入比特币生态系统。通过借鉴BRC-20的理念和原则，BRC-721扩展了比特币网络上代币化的能力，包括非同质化代币，从而实现更广泛的数字资产管理和价值表现。

BRC-721专为比特币网络上的非同质化代币（NFT）设计。它允许在比特币区块链上创建、拥有和转移独特的数字资产。在BRC-721下创建的每个代币都有一个唯一的标识符，使它们具有独特性且不可互换。

## 操作

### 部署 brc-721

部署一个brc-721 NFT，并使用外部链接为每个token提供图片和属性等信息：

``` json
{
    "p": "brc-721",
    "op": "deploy",
    "tick": "ordinals",
    "max": "10000",
    "buri": "https://ipfs.io/abc/"
}
```

* 对于token ID为1的代币，其元数据位于`https://ipfs.io/abc/1`

部署一个brc-721 NFT，并将NFT元数据保存在链上：

``` json
{
    "p": "brc-721",
    "op": "deploy",
    "tick": "ordinals",
    "max": "10000",
    "meta": {
        "name": "Ordinals",
        "description": "Bring NFT to Bitcoin", 
        "image": "https://storage.googleapis.com/opensea-prod.appspot.com/puffs/3.png",
        "attributes": [
            {
                "trait_type": "trait1", 
                "value": "value1"
            }, ... ]
    }
}
```

* 此时NFT中的每一个token都使用同一个元数据

| Key | 必填? | 描述 |
|---|---|---|
| p | 是 | 协议：帮助其他系统识别和处理brc-721事件 |
| op | 是 | 操作：事件类型（部署，铸造，更新）|
| tick | 是 | 标识符：brc-721的标识符，4到8个字母，不区分大小写 |
| buri | 否 | 基础URI：brc-721的基础URI，通过访问`{buri}{token_id}`获取代币的元数据 |
| meta | 否 | 集合的元数据 |
| max | 是 | 最大供应量：设置brc-721的最大供应量 |

* `buri`或`meta`应至少填写一个，`meta`优先

#### Metadata

| Key | 必填? | 描述 |
|---|---|---|
| name | 是 | token名称 |
| description | 是 | token描述 |
| image | 是 | token图片链接，或者自描述的展示数据，比如：`data:image/svg+xml;base64,<base64_encoded_image_bytes>` |
| attributes | 否 | token属性 |

* 关于代币URI和元数据的更多信息，请参考[EIP-721](https://eips.ethereum.org/EIPS/eip-721)和[元数据标准](https://docs.opensea.io/docs/metadata-standards)

### 铸造 brc-721

``` json
{
    "p": "brc-721",
    "op": "mint",
    "tick": "ordinals"
}
```

| Key | 必需？ | 描述 |
|---|---|---|
| p | 是 | 协议：帮助其他系统识别和处理brc-721事件 |
| op | 是 | 操作：事件类型（deploy, mint, update） |
| tick | 是 | Ticker：brc-721的标识符，4到8个字母，不区分大小写 |

* 代币ID根据铭文ID的顺序从1到`max`，比如，第一个mint成功的token ID为1

### 转移 brc-721

转移brc-721代币非常简单，只需将上面铸造的铭文发送给接收者。无需像brc-20那样在发送前铸造一个转移铭文。

### 修改 brc-721

``` json
{
    "p": "brc-721",
    "op": "update",
    "tick": "ordinals",
    "buri": "https://ipfs.io/abc"
}
```

| Key | 必需？ | 描述 |
|---|---|---|
| p | 是 | 协议：帮助其他系统识别和处理brc-721事件 |
| op | 是 | 操作：事件类型（部署，铸造，更新） |
| tick | 是 | Ticker：brc-721的标识符，4到8个字母，不区分大小写 |
| buri | 否 | BaseURI：brc-721的基本URI，访问`{buri}{token_id}`获取某个代币的元数据 |

* 目前仅支持修改`buri`

* 此操作仅允许拥有部署铭文的部署者执行

## 状态变更

* NFT Deployer

  * 持有deploy inscription的地址即为deployer
  * 首次铸造deploy inscription的接收地址即为deployer
  * 如果deploy inscription转移到新的地址，则新地址为deployer
  * deployer可以修改`buri`

* Token ID

  * 与ERC721类似，BRC-721每个collection的token都具有唯一ID
  * 每个`mint`操作的inscription，将产生一个token，按照inscription ID的顺序，token ID从1到`max`（deploy inscription中定义的总量）
  * 超过总量后mint inscription无效

* Token Owner

  * 持有mint inscription的地址即为该token的owner
  * mint inscription转移给新的地址后，owner变更为新地址

* Transfer 转账

  * 通过`ord wallet send <ADDRESS> <INSCRIPTION ID>`转移该NFT token

## 常见问题解答

### brc-721与原生ordinals NFT有何不同？

brc-721是在ordinals协议基础上构建的。尽管ordinals NFT本身可以存储图片，但brc-721与原生ordinals NFT之间存在显著的功能差异：

* 数据存储

  * 原生ordinals NFT的每个token都需要保存图片，会导致mint费用过高、占用大量Bitcoin网络空间等问题；
  * 而brc-721只需要deploy时保存一次图片，mint操作并不需要保存图片，因此可以极大地节省mint费用和Bitcoin网络空间；同时，brc-721也支持将图片保存在IPFS等链下服务中，不仅能节省Bitcoin空间，也能为每个Token提供灵活的属性信息；
  * 原生ordinals NFT无法针对Collection进行有效索引，而brc-721通过提供类似brc-20的JSON规范，能够有效索引和查找同一个Collection的NFT。

* brc协议的兼容性

  * brc-721采用与brc-20类似的协议格式，通过JSON内容定义不同的功能，极大地提高了NFT的灵活性。例如，可以通过update操作实现reveal功能；通过tick字段可以有效地对一个Collection的NFT进行索引。

* NFT生态的兼容性

  * erc-721标准的NFT在当前市场更受欢迎。brc-721采用的token URI与metadata规范与erc-721保持一致，可以快速地与现有NFT生态进行适配。同时，原生ordinals不支持trait等字段，而brc-721可以支持定义NFT属性和稀有度等信息。

### brc-721与brc-20有何不同？

  * brc-721用于Non-Fungible Token (NFT)，而brc-20用于Fungible Token
  * brc-721遵循brc-20的规范，利用JSON定义token和function
  * brc-20在transfer时需要先mint inscription，这是FT的需要，但会导致transfer成本过高，并增加Bitcoin网络中无效信息，而brc-721直接利用ordinals inscription的特性，直接send就能完成transfer，极大降低了成本，也减少网络无效信息。

### brc-721与brc721.com有何不同？

[brc721.com](https://brc721.com/) 也提出了一个基于ordinals协议的非同质化代币（NFT）方案，但其协议更为复杂，且与brc-20不兼容。而brc-721与brc-20标准保持一致。

## 贡献

BRC-721是一个实验性标准，将非同质化代币（NFT）引入比特币网络。有了这个标准，用户可以创建、铸造、转移和更新独特的数字资产，支持广泛的应用场景，如数字艺术、收藏品、虚拟商品等。

该标准允许一系列操作来方便非同质化代币的管理，包括部署、铸造、转移和更新元数据。每个代币分配一个唯一的标识符，确保每个NFT具有独特性，不能与另一个NFT一对一地交换。

作为一个实验性标准，BRC-721欢迎改进和修改，以增强其功能，适应不断发展的NFT生态系统需求。

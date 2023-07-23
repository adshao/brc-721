[English](./README.md) | [中文](./README_zh.md)

---

# BRC-721：比特币网络上的非同质化代币

BRC-721的灵感来源于BRC-20，这是一个在比特币网络上创建可替代代币的实验性标准。BRC-20的详细信息可以在[brc-20-experiment](https://domo-2.gitbook.io/brc-20-experiment/)中找到，其目标是将发行同质化代币的功能引入比特币生态系统。通过借鉴BRC-20的理念和原则，BRC-721扩展了比特币网络上代币化的能力，包括非同质化代币，从而实现更广泛的数字资产管理和价值表现。

BRC-721专为比特币网络上的非同质化代币（NFT）设计。它允许在比特币区块链上创建、拥有和转移独特的数字资产。在BRC-721下创建的每个代币都有一个唯一的标识符，使它们具有独特性且不可互换。

## 操作

### 部署 brc-721

例1，部署一个brc-721 NFT，并使用外部链接为每个token提供图片和属性等信息：

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

例2，部署一个brc-721 NFT，并设置签名公钥，用于验证铸造铭文的签名：

``` json
{
    "p": "brc-721",
    "op": "deploy",
    "tick": "ordinals",
    "max": "10000",
    "sig": {
      "pk": "04E32DF42865E97135ACFB65F3BAE71BDC86F4D49150AD6A440B6F15878109880A0A2B2667F7E725CEEA70C673093BF67663E0312623C8E091B13CF2C0F11EF652",
      "fields": ["rec", "uid", "exph"]
    },
    "buri": "https://ipfs.io/abc/"
}
```

* 指定后续铸造铭文的签名必须使用 `sigpk` 指定的私钥进行签名，否则铭文无效

例3，部署一个brc-721 NFT，将NFT元数据保存在链上，并设置为不可修改：

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
    },
    "upd": false
}
```

* 此时NFT中的每一个token都使用同一个元数据

#### 铭文规格

| Key | 必填? | 描述 |
|---|---|---|
| p | 是 | 协议：帮助其他系统识别和处理brc-721事件 |
| op | 是 | 操作：事件类型（部署，铸造，更新）|
| tick | 是 | 标识符：brc-721的标识符，不少于4个字母，不区分大小写 |
| max | 是 | 最大供应量：设置brc-721的最大供应量 |
| upd | 否 | 是否允许修改元数据，默认值 `true` |
| buri | 否 | 基础URI：brc-721的基础URI，通过访问`{buri}{token_id}`获取代币的元数据 |
| meta | 否 | 集合的元数据 |
| sig | 否 | 指定铭文的签名公钥信息，用于验证铸造铭文的签名 |

* 当且仅当 `upd` 为 `true`时，部署者才能通过发送 `update` 铭文来更新集合的元数据；否则，元数据是不可变的，后续任何 `update` 操作都将无效。

#### meta 规格

| Key | 必填? | 描述 |
|---|---|---|
| name | 是 | token名称 |
| description | 是 | token描述 |
| image | 否 | token图片链接，或 [Data URL Scheme](https://en.wikipedia.org/wiki/Data_URI_scheme) 格式，如：`data:image/svg+xml;base64,<base64_encoded_image_bytes>` |
| attributes | 否 | token属性 |

* 关于代币URI和元数据的更多信息，请参考[EIP-721](https://eips.ethereum.org/EIPS/eip-721)和[元数据标准](https://docs.opensea.io/docs/metadata-standards)

#### sig 规格

| Key | 必填? | 描述 |
|---|---|---|
| pk | 是 | 签名公钥：用于验证铸造铭文的签名，Compressed/Uncompressed格式，十六进制表示 |
| fields | 否 | 签名字段：用于签名的字段，可选值：`uid`、`expt`、`exph`，默认值 `[]` |
| rec | 否 | 铭文接收者的地址，如果 `rec` 不是铭文的接收者，则铭文无效 |
| uid | 否 | 签名使用的 `uid` 字段是否唯一，则后续所有铸造铭文的 `uid` 字段必须唯一，如果重复 `uid` 的铭文则无效 |
| expt | 否 | 签名使用的 `expt` 字段是否有效，则后续所有铸造铭文的 `expt` 字段必须大于铭文所在区块的时间 `block_time`，否则铭文无效 |
| exph | 否 | 签名使用的 `exph` 字段是否有效，则后续所有铸造铭文的 `exph` 字段必须大于铭文所在区块的高度 `block_height`，否则铭文无效 |

* 如果指定了 `pk`，则后续铸造铭文的签名必须使用 `pk` 指定的私钥进行签名，否则铭文无效

### 铸造 brc-721

例1，铸造铭文：

``` json
{
    "p": "brc-721",
    "op": "mint",
    "tick": "ordinals"
}
```

例2，如果部署时指定了 `sig`，则需铸造经过签名的铭文：

``` json
{
    "p": "brc-721",
    "op": "mint",
    "tick": "ordinals",
    "sig": {
      "s": "",
      "uid": "",
      "expt": ""
    }
}
```

#### 铭文规格

| Key | 必需？ | 描述 |
|---|---|---|
| p | 是 | 协议：帮助其他系统识别和处理brc-721事件 |
| op | 是 | 操作：事件类型（deploy, mint, update） |
| tick | 是 | Ticker：brc-721的标识符，4到8个字母，不区分大小写 |
| sig | 否 | 签名：用于验证铸造铭文的签名，如果部署时指定了 `sig`，则铭文必须经过签名 |
| s | 否 | DER编码的签名，十六进制表示 |
| uid | 否 | 签名使用的 `uid` 字段，用于唯一指定当前铭文的 `uid`，如果 `uid` 重复，则铭文ID（token ID）最小的铭文生效，其他重复 `uid` 的铭文无效 |
| expt | 否 | 签名使用的 `expt` 字段，用于指定当前铭文的 `expt`，如果 `expt` 小于铸造铭文所在区块的时间 `block_time`，则该铸造铭文无效 |
| exph | 否 | 签名使用的 `exph` 字段，用于指定当前铭文的 `exph`，如果 `exph` 小于铸造铭文所在区块的高度 `block_height`，则该铸造铭文无效 |

* 代币ID根据铭文ID的顺序从1到`max`，比如，第一个mint成功的token ID为1

##### 签名规格

如果在部署时指定了 `sig`，则铸造铭文时必须经过签名，签名规格如下：

* 如果在部署铭文 `sig` 的 `fields` 字段指定了 `rec`，则该字段为铸造铭文的接收者地址，无需在铭文中单独指定该字段
* 原始签名信息 Message：将 `fields` 中指定的字段按照字典序排列，然后按照 `key=value` 的格式拼接，如：`exph=100&rec=xxx&uid=xxx`
  * 如果 `key` 不在 `fields` 中，则不包含在原始签名信息中
* 将原始签名信息经过两次SHA256哈希，得到哈希值 `h`
* 使用 `sig.pk` 指定的私钥对哈希值 `h` 进行 ECDSA secp256k1 签名，得到签名 `s`，将 `s` 转换为DER编码，得到 `sig.s`

### 转移 brc-721

转移brc-721代币非常简单，只需将上面铸造的铭文发送给接收者。无需像brc-20那样在发送前铸造一个转移铭文。

### 修改 brc-721

``` json
{
    "p": "brc-721",
    "op": "update",
    "tick": "ordinals",
    "upd": false,
    "buri": "https://ipfs.io/abc/",
    "meta": { ... }
}
```

#### 铭文规格

| Key | 必需？ | 描述 |
|---|---|---|
| p | 是 | 协议：帮助其他系统识别和处理brc-721事件 |
| op | 是 | 操作：事件类型（部署，铸造，更新） |
| tick | 是 | Ticker：brc-721的标识符，4到8个字母，不区分大小写 |
| upd | 否 | 是否允许修改元数据，默认值 `true` |
| buri | 否 | BaseURI：brc-721的基本URI，访问`{buri}{token_id}`获取某个代币的元数据 |
| meta | 否 | 集合的元数据 |

* 此操作仅允许拥有 `deploy` 铭文的地址执行
* `update` 铭文必须由 `deploy` 铭文地址铸造，并发送自己
* 仅 `upd`、`buri` 和 `meta` 字段允许被修改
* 如果 `upd` 为 `true`，铭文部署者可以通过发送 `update` 铭文来更新集合的元数据；否则，元数据是不可变的，在 `upd` 设置为 `false` 的铭文后的任何 `update` 操作都将无效。

## 状态变更

* NFT Deployer

  * 持有deploy inscription的地址即为deployer
  * 首次铸造deploy inscription的接收地址即为deployer
  * 如果deploy inscription转移到新的地址，则新地址为deployer

* Token ID

  * 与ERC721类似，BRC-721每个collection的token都具有唯一ID
  * 每个`mint`操作的inscription，将产生一个token，按照inscription ID的顺序，token ID从1到`max`（deploy inscription中定义的总量）
  * 超过总量后mint inscription无效
  * `mint` 铭文ID必须大于 `deploy` 铭文ID

* Token Owner

  * 持有`mint` inscription的地址即为该token的owner
  * mint inscription转移给新的地址后，owner变更为新地址

* Transfer 转账

  * 通过`ord wallet send <ADDRESS> <INSCRIPTION ID>`转移该NFT token

* 元数据

  * 当 `upd` 为 `false` 时，元数据是不可变的，之后的任何 `update` 铭文都将无效
  * 当 `upd` 为 `true` 时，元数据允许被修改
  * 铭文的元数据最初由 `deploy` 铭文设定
  * 通过 `update` 铭文可以修改元数据
  * `update` 铭文必须由 `deploy` 铭文地址铸造，并发送给自己
  * `update` 铭文ID必须大于 `deploy` 铭文ID

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

## 修订记录

### v0.0.3

* 增加 `sig` 等字段，用于验证铭文的签名，提供白名单功能

### v0.0.2

* 增加 `upd` 字段，用于控制元数据是否可修改

### v0.0.1

* 初始版本

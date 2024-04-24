# NFT (TODO: Token Name)

In order to automatically and transparently manage all the real-estates within the Briky Land ecosystem, we utilize some of the most novel technologies recently, **Blockchain and Non-fungible Token** **(NFT)**.

{% hint style="info" %}
Non-Fungible Tokens (NFTs) are unique digital assets that are indivisible and cannot be replicated. Unlike cryptocurrencies like Bitcoin or Ethereum, which are fungible and can be exchanged on a one-to-one basis, NFTs represent ownership or proof of authenticity of a specific item or piece of content. These items can range from digital art, music, videos, in-game items, collectibles, virtual assets, and more. In our case, it presents real estate possession.
{% endhint %}

## Token Standards

Based on specific business requirements, it appears that [**ERC-1155**](https://eips.ethereum.org/EIPS/eip-1155) standard is the most suitable for us to implement our tokens.

{% hint style="info" %}
**ERC** stands for Ethereum Request for Comments. It's a formal method used to propose improvements, standards, or changes to the Ethereum network. ERCs are documents that outline specific technical standards or protocols for Ethereum-based projects. The two most noticeable standards are ERC-20 (fungible tokens) and ERC-721 (non-fungible tokens).
{% endhint %}

ERC-1155 is the combination of ERC-20 (fungible token) and ERC-721 (non-fungible token). Following the standards, each token will have an identification, but multiple tokens can share the same one. Its method helps us display the share of ownership of any particular real estate between multiple people.

A certain real estate that is legally delegated to Briky Land shall be minted into a new set of tokens with the same index that identifies the asset in the database. The minted amount is fixed as **\[TODO: should be fixed number]** and belongs to the registered Ethereum account. From here, through smart contract functions, the ownership of the tokens can be transferred totally or partially between on-chain accounts.

Any balance updates of these tokens can be observed transparently across the blockchain where its smart contract is deployed.

Additionally, standard [**ERC-2981**](https://eips.ethereum.org/EIPS/eip-2981) is also implemented as it is a standardized way to retrieve royalty payment information for non-fungible tokens (NFTs) to enable universal support for royalty payments across all NFT marketplaces and ecosystem participants.

## Administration

Only the Ethereum account that represents Briky Land's system can decide to mint or burn a set of tokens.&#x20;

Whenever a new real estate joins our ecosystem, our admin account will proceed to mint a new batch of tokens with the index and URI representing the asset in our database. These data are immutable to protect the transparency.&#x20;

Whenever a participating real estate leaves our ecosystem, our admin account will deprecate all of its tokens, and the balance of any holder will be set back to 0.&#x20;

Whenever the state of the real estate requires an update, our admin account will deprecate the old tokens and mint a new batch with a new index and a new URI, following the immutability of the tokens.&#x20;

Any further exchange depends sorely on token holders without any adminship.&#x20;

Thanks to the advantages of blockchain, all the administrative interactions are observable.

## **Implementation**

### Events

```
event TransferSingle(
    address indexed operator,
    address indexed from,
    address indexed to,
    uint256 id,
    uint256 value
);
```

Emitted when `value` tokens of token type `id` are transferred from `from` to `to` by `operator`.



```
event TransferBatch(
    address indexed operator,
    address indexed from,
    address indexed to,
    uint256[] ids,
    uint256[] values
);
```

Equivalent to multiple `TransferSingle` events, where `operator`, `from` and `to` are the same for all transfers.



```
event ApprovalForAll(
    address indexed account, 
    address indexed operator, 
    bool approved
);
```

Emitted when `account` grants or revokes permission to `operator` to transfer their tokens, according to `approved`.



```
event URI(string value, uint256 indexed id);
```

Emitted when the URI for token type `id` changes to `value`, if it is a non-programmatic URI.



### Functions

```
function balanceOf(address account, uint256 id) external view returns (uint256);
```

Returns the amount of tokens of token type `id` owned by `account`.



```
function balanceOfBatch(
    address[] calldata accounts, 
    uint256[] calldata ids
) external view returns (uint256[] memory)
```

Batch version of `balanceOf`.



```
function setApprovalForAll(address operator, bool approved) external;
```

Grants or revokes permission to `operator` to transfer the caller's tokens, according to `approved`. Emits an `ApprovalForAll` event.



```
function isApprovedForAll(
    address account, 
    address operator
) external view returns (bool);
```

Returns true if `operator` is approved to transfer `account`'s tokens.



```
function safeTransferFrom(
    address from, 
    address to, 
    uint256 id, 
    uint256 amount, 
    bytes calldata data
) external;
```

Transfers `amount` tokens of token type `id` from `from` to `to`. Emits a `TransferSingle` event.



<pre><code><strong>function safeBatchTransferFrom(
</strong><strong>        address from,
</strong><strong>        address to,
</strong>        uint256[] calldata ids,
        uint256[] calldata amounts,
        bytes calldata data
) external;
</code></pre>

Batch version of `safeTransferFrom`.



```
function uri(uint256 id) external view returns (string memory);
```

Returns the URI for token type `id`.



```
function royaltyInfo(
        uint256 tokenId,
        uint256 salePrice
) external view returns (address receiver, uint256 royaltyAmount);
```

Returns how much royalty is owed and to whom, based on a sale price that may be denominated in any unit of exchange. The royalty amount is denominated and should be paid in that same unit of exchange.



```
function mint(
    address[] calldata receivers,
    uint256[] calldata amounts,
    string calldata uri
) external returns (uint256)
```

Returns the token index of a newly minted token for multiple `receivers` with multiple `amounts`, respectively as well as a single common `uri`.  Only the contract owner can call this function.



```
function deprecate(uint256 id) external;
```

Only the contract owner can call this function to deprecate all tokens of a same `id`.

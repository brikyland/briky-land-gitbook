# Treasury

## Overview

All the value collected from offering tokens through auction should be locked into a public vault, which we call the Treasury. It is implemented as an on-chain smart contract.

The value shall be split into 5 different funds, described in the below chart:

<figure><img src="../../.gitbook/assets/image.png" alt=""><figcaption></figcaption></figure>

Each of the four first funds will have a different owner account who can decide to withdraw from the funds for specific tasks following its mission. The last fund is used for the liquidity of **BRIK** tokens. This fund is interacted directly by 2 other smart contracts:

* [Token **BRIK** smart contract](./) withdraws value from the **Treasury** to refund token holders when they liquidate their tokens.
* [**Auction** smart contract](auction.md) transfers value into the **Treasury** whenever an account bids during auctions.&#x20;

All these value transfers are observable across the chain the contract is deployed, ensuring the transparency of the money vault.

## Implementation

During implementation, we will name funds as follows:

* **Operation Fund:** Fund for Operation and Management.
* **Marketing Fund:** Fund for Marketing and PR.
* **Blockchain Fund:** Fund for Developing Blockchain & NFT Product.
* **Platform Fund:** Fund for Developing Platform.
* **Liquidity:** Liquidity pool of token **BRIK**.

### Events

```
event BlockchainFundWithdrawal(uint256 amount);
```

Emitted when `BlockchainFundOwner` withdraw `amount` value from **Blockchain Fund**.



```
event MarketingFundWithdrawal(uint256 amount);
```

Emitted when `MarketingFundOwner` withdraw `amount` value from **Marketing Fund**.



```
event OperationFundWithdrawal(uint256 amount);
```

Emitted when `OperationFundOwner` withdraw `amount` value from **Operation Fund**.



```
event PlatformFundWithdrawal(uint256 amount);
```

Emitted when `PlatformFundOwner` withdraw `amount` value from **Platform Fund**.



```
event LiquidityWithdrawal(uint256 amount);
```

Emitted when token **BRIK** contract withdraws `amount` value from **Liquidity Pool**.



```
event LiquidityProvision(uint256 amount);
```

Emitted when  token **BRIK** contract provide `amount` value into **Liquidity Pool**.

### Functions

```
function currency() external view returns (address);
```

Returns the address of the token **ERC-20** that is used as liquidity currency.



```
function operationFund() external view returns (uint256);
```

Returns the value of **Operation Fund**.



```
function operationFundOwner() external view returns (address);
```

Returns the owner address of the **Operation Fund**.



```
function withdrawOperationFund(uint256 amount) external;
```

Only `OperationFundOwner` can call this function to withdraw `amount` value from **Operation Fund**. Emits event `OperationFundWithdrawl`.



```
function marketingFund() external view returns (uint256);
```

Returns the value of **Marketing Fund**.



```
function marketingFundOwner() external view returns (address);
```

Returns the owner address of the **Marketing Fund**.



```
function withdrawMarketingFund(uint256 amount) external;
```

Only `MarketingFundOwner` can call this function to withdraw `amount` value from **Marketing Fund**. Emits event `MarketingFundWithdrawl`.



```
function blockchainFund() external view returns (uint256);
```

Returns the value of **Blockchain Fund**.



```
function blockchainFundOwner() external view returns (address);
```

Returns the owner address of the **Blockchain Fund**.



```
function withdrawBlockchainFund(uint256 amount) external;
```

Only `BlockchainFundOwner` can call this function to withdraw `amount` value from **Blockchain Fund**. Emits event `BlockchainFundWithdrawl`.



```
function platformFund() external view returns (uint256);
```

Returns the value of Platform Fund.



```
function platformFundOwner() external view returns (address);
```

Returns the owner address of the Platform Fund.



```
function withdrawPlatformFund(uint256 amount) external;
```

Only `PlatformFundOwner` can call this function to withdraw `amount` value from Platform Fund. Emits event `PlatformFundWithdrawl`.



```
function liquidity() external view returns (uint256);
```

Returns the value of the **Liquidity Pool**.



```
function provideLiquidity(uint256 amount) external;
```

Provide `amount` value into the **Liquidity Pool**. The value shall be split into funds. Emits event `LiquidityProvision`.



```
function withdrawLiquidity(uint256 amount) external;
```

Only token **BRIK** contract can call this function to withdraw `amount` value from the **Treasury** to refund tokenholders when they liquidate their **BRIK**. Emits event `LiquidityWithdrawal`.

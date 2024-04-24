# Auction

## Overview

Token **BRIK** will be publicly offered through auctions. The smart contract **Auction** will be implemented to automatically and transparently process these auctions.

In each auction, a certain amount of **BRIK** tokens will be minted and locked within the **Auction** contract. Until each auction ends, any Ethereum accounts can deposit **\[TODO: chose ERC-20 currency]** multiple times into it. Once the time is out, the locked **BRIK** shall be shared proportionally among all depositors.

Detailedly, let's assume a general depositor $$i$$ deposits $$d_i$$$$(v_i > 0)$$ value in total during an auction that locks to offer $$X$$ token **BRIK**. When the auction concludes, the depositor will receive the amount of **BRIK** equal to:

$$
X \frac{d_i}{\sum{d_j}}
$$

When an auction ends with no one depositing value, the locked tokens shall be burnt.

There is only at most one auction proceeding at a time and only the contract admin can call it. When an auction is going to happen, the **Auction** contract itself will request minting new tokens from the [token **BRIK** contract](./). All the value deposited will immediately be transferred to the [**Treasury**](treasury.md).

## Implementation

### Events

```
event NewAuction(
    uint256 indexed auctionId, 
    uint256 amount, 
    uint256 endAt
);
```

Emitted when a new auction is created with the index `auctionId`, which will end at `endAt` too publicly offer `amount` of token **BRIK**.



```
event AuctionRefund(uint256 indexed auctionId);
```

Emitted when an auction ended with no depositors and its **BRIK** locked is requested to be burnt.



```
event Deposit(
    uint256 indexed auctionId, 
    address indexed account, 
    uint256 amount
);
```

Emitted when `account` deposits `amount` value into auction indexed `auctionId`.



```
event Withdrawal(address indexed account, uint256 amount);
```

Emitted when `account` withdraw `amount` of **BRIK** tokens won from auctions.



### Functions

```
function auctionNumber() external view returns (uint256);
```

Returns the number of created auctions which also is the index of the current auction.



```
function currency() external view returns (address);
```

Returns the address of the token **ERC-20** that is used as depositing currency.



```
function getAuctionInfo(uint256 auctionId) external view returns (uint256, uint256, uint256)
```

Returns the data of the auction indexed `auctionId` with its offered **BRIK** amount, total value deposited, and ending timestamp, respectively.



```
function withdrawableTokenAmountOf(address account) external view returns (uint256 amount);
```

Returns the amount of **BRIK** won of `account` that haven't been collected from previous auctions.



```
function createAuction(uint256 amount, uint256 endAt) external;
```

Only the contract owner can call this function to request a new auction with `amount` newly minted **BRIK**, which will conclude at `endAt`. Can only create a new auction when there isn't any ongoing. Emits event `NewAuction`.



```
function refundVacantAuction(uint256 auctionId) external;
```

Request to burn all **BRIK** tokens from the auction indexed `auctionId` if it has no depositor. Emits event `AuctionRefund`.



```
function deposit(uint256 amount) external;
```

Deposit `amount` value into the current auction. Emits event `Deposition`.



```
function withdraw() external;
```

Withdraw all **BRIK**  won from previous auctions that haven't been collected. Emits event `Withdrawal`.

# Token BRIK

## **Overview**

**BRIK** is an [**ERC-20**](https://eips.ethereum.org/EIPS/eip-20) token that will be the major crypto-currency to exchange in Briky Land's ecosystem. Besides the ordinary functions following the standard, its tokenomics includes public offering by [**Auction**](auction.md), liquidity [**Treasury**](treasury.md), and a stake token [**BRIKI**](staking-brik-receiving-briki.md).

{% hint style="info" %}
**ERC-20** is widely known as the standard for Fungible Tokens on Ethereum-based blockchains, whose each unit is identical and interchangeable, making them ideal for representing currencies, fungible assets, or utility tokens.
{% endhint %}

## Token Issuance

### Minting

Token **BRIK** can be minted in 2 scenarios:

* Requested by [the **Auction** contract](auction.md) for public offering through auctions. The auction will sell tokens to collect liquidity into the **Treasury.**
* Requested by [the stake token **BRIKI** contract](staking-brik-receiving-briki.md) for daily rewards to stakeholders during the 6 first years of the project. The amounts of tokens issued each day are predefined in the contracts following the plan below.

**TODO: discuss and finish the table**

<table><thead><tr><th>Phase</th><th>Issuance (daily)</th><th data-type="number">Issuance (annually)</th><th>Annual Percentage Yield </th></tr></thead><tbody><tr><td>Initial minting</td><td></td><td>null</td><td></td></tr><tr><td>1st Year</td><td></td><td>null</td><td></td></tr><tr><td>2nd Year</td><td></td><td>null</td><td></td></tr><tr><td>3rd Year</td><td></td><td>null</td><td></td></tr><tr><td>4th Year</td><td></td><td>null</td><td></td></tr><tr><td>5th Year</td><td></td><td>null</td><td></td></tr><tr><td>6th Year</td><td></td><td>null</td><td></td></tr></tbody></table>

The total supply of **BRIK** is capped at the threshold of **\[TODO: MAX\_CAP]**.

### Burning

Token **BRIK** can be burnt in 2 scenarios:

* Requested by [the **Auction** contract](auction.md) to conclude an auction with no depositors.
* Requested by token holders to liquidate **BRIK**. The liquidation of a certain **BRIK** amount is described in the formula:

$$
liquidation(value) = \frac{value * liquidity}{totalSupply}
$$

There, `value` is the amount of liquidated **BRIK**, `totalSupply` is the total amount of **BRIK** in existence, and `liquidity` is the value locked in the **Treasury.**

## Implementation

### Events

```
event Transfer(address indexed from, address indexed to, uint256 value);
```

Emitted when a `value` amount transferred from address `from` to address `to`.



```
event Approval(address indexed owner, address indexed spender, uint256 value);
```

Emitted when address `spender` set to have `value` allowance on address `owner`.



```
event Liquidation(address indexed account, uint256 amount, uint256 liquidity);
```

Emitted when address `account` liquidate `amount` of **BRIK** to retrieve `liquidity` value.

### &#x20;Functions

```
function name() external view returns (string memory);
```

Returns the name of the token. Which is `Briky`.



```
function symbol() external view returns (string memory);
```

Returns the symbol of the token. Which is `BRIK`.



<pre><code><strong>function decimals() external view returns (uint8);
</strong></code></pre>

Returns decimals places of the token. Which is `18`.



```
function totalSupply() external view returns (uint256);
```

Returns the total amount of **BRIK** that has been staked into the pool.



```
function balanceOf(address account) external view returns (uint256)
```

Returns the value of **BRIK** owned by `account`, which is equal to the amount of **BRIK** possessed but sealed in the pool.



```
function transfer(address to, uint256 value) external returns (bool);
```

Moves a `value` amount of **BRIK** from the caller's account to address `to`, which also presents an equivalent capability of unstaking to retrieve **BRIK**. Returns a boolean value indicating whether the operation succeeded. Emits event `Transfer`.



```
function transferFrom(address from, address to, uint256 value) external returns (bool);
```

Moves a `value` amount of **BRIK** from `from` to address `to` using the allowance mechanism. `value` is then deducted from the caller's allowance. Returns a boolean value indicating whether the operation succeeded. Emits event `Transfer`.



```
function allowance(address owner, address spender) external view returns (uint256)
```

Returns the remaining number of **BRIK** that `spender` will be allowed to spend on behalf of `owner` through `transferFrom`. The allowance doesn't increase together with the balance.



```
function approve(address spender, uint256 value) external returns (bool);
```

Sets a `value` amount of **BRIK** as the allowance of `spender` over the caller's tokens. Emits event `Approval`.



```
function mint(address account, uint256 amount) external;
```

Only the **Auction** contract and the **BRIKI** contract can call this function to mint `amount` of newly **BRIK** tokens. Emits event `Transfer`.



```
function burn(address account, uint256 amount) external;
```

Only the **Auction** contract can call this function to burn `amount` of **BRIK**. Emit event `Transfer`.



```
function liquidate(uint256 amount) external;
```

Burns `amount` **BRIK** tokens from the caller's balance and transfer back the equivalent liquidity.

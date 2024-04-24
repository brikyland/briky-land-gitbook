# Staking BRIK, Receiving BRIKI

## Overview

In order to protect the liquidity of **BRIK** in the early stage of the project, we incentivize token holders to deposit their **BRIK** into our Staking Pool, which seals your tokens but helps grow the volume every day until you wish to withdraw them.

Sealing money still sounds like killing its utilities? Don't worry, we replace such utilities by returning to stakeholders the exact same amount of another derivative token, called **BRIKI**, as the amount of **BRIK** has been staked. This **BRIKI** can be used like an ordinary [**ERC-20**](https://eips.ethereum.org/EIPS/eip-20) token to transfer the capability to withdraw staked tokens from the Staking Pool. Moreover, the balance of **BRIKI** will grow daily as the corresponding volume of **BRIK** will grow inside the pool as well.

Concisely, interactions with the Staking Pool should be like:

* Through our app, any user stakes their **BRIK** into the Staking Pool, implemented as a smart contract. These amounts of **BRIK** will be locked, while equivalent amounts of **BRIKI** will be transferred back to the corresponding stakeholders. These **BRIKI** can be spent with a liquidity rate of 1:1 with **BRIK**.
* Within **\[6 years]** since our pool deployed, a predefined amount of **BRIK** will be issued into the Staking Pool, while the same amount of **BRIKI** shall be issued to all stakeholders' balance, directedly and proportionally to their shares.
* Through our app, any stakeholder, or in other words, any **BRIKI** holder can withdraw their stake in **BRIK** and burn an equivalent amount of **BRIKI**.

## The Mathematics Behind

On day $$k$$ $$(k > 0)$$, the **BRIK** contract will be triggered to issue a predefined $$r_k$$ $$(r_k > 0)$$ tokens into the **Staking Pool** contract. These tokens will be shared among stakeholders correspondingly to their share within the total value $$V_k$$ $$(V_k > 0)$$ locked inside the pool. The stake inflation of each stakeholder is proportional to every other, and also to the inflation of the whole pool.

### **Simple scenario**

Consider an arbitrary **BRIK** holder who would like to stake exactly once with $$v$$ tokens on the $$t$$-th day since the **Staking Pool** deployed. Today, the $$T$$-th day $$(0 \le t \le T)$$, his stake will increase following the traditional **Compound Interest** formula:

$$
reward(v, t, T)  = v\prod^{T-1}_{i=t}\left(1 + \frac{r_i}{V_i}\right)
$$

{% hint style="info" %}
A formula like this is impractical to implement on smart contracts, due to its loop of multiplication can grow enormously while the gas being charged on the complexity of the called function.
{% endhint %}

It requires to proceed a few more transformations. Let $$P_k$$ to be the accumulated product of all daily interest rates from day $$0$$ to day  $$k-1$$:&#x20;

$$
P_k = \begin{cases}
1 & \quad k = 0 \\
P_{k-1}\left(1 + \frac{r_{k-1}}{V_{k-1}}\right) = \prod^{k-1}_{i=0}\left(1 + \frac{r_i}{V_i}\right) & \quad k \ge 1
\end{cases}
$$

The value of $$P_k$$ can be calculated easily and only once every day as to being stored globally. With $$P_k$$, we can simplify the first formula:

$$
reward(v, t, T)  = v\prod^{T-1}_{i=t}\left(1 + \frac{r_i}{V_i}\right)= v\frac{\prod^{T-1}_{i=0}\left(1 + \frac{r_i}{V_i}\right)}{\prod^{t-1}_{i=0}\left(1 + \frac{r_i}{V_i}\right)} = v\frac{P_T}{P_t}
$$

### General Scenario

Now, consider staking multiple times. Due to the reward of each occurrence of staking being independent, the whole stake simply is the sum of all singular rewards. Generally, on day $$T$$ an address that has staked $$N$$different times will possess the total stake:

$$
\begin{align*}
stake(T)
& = \sum^{N-1}_{i=0} reward(v_i, t_i, T) \\
& = \sum^{N-1}_{i=0} v_i \frac{P_T}{P_{t_i}} \\
& = P_T \sum^{N-1}_{i=0} \frac{v_i}{P_{t_i}}
\end{align*}
$$

{% hint style="success" %}
To this point, the formula has become more clean for implementation. The input parameter $$T$$ has been isolated from the loop while the rest can be easily precomputed and stored. Therefore, it is possible to create functions to update individual stake values by individual stored factors or to update the whole group of stakeholders through some global values, each with a complexity of $$O(1)$$.
{% endhint %}

## Implementation

As aforementioned, the Staking Pool also takes charge of issuing a derivative token for replacement, called **BRIKI**. From the point of view of smart contract development, **BRIKI** should comply with the [**ERC-20**](https://ethereum.org/en/developers/docs/standards/tokens/erc-20/) standard and be the pool itself.

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
event Stake(address indexed account, uint256 value);
```

Emitted when address `account` stakes `value` **BRIK**.



```
event Unstake(address indexed account, uint256 value, uint256 fee);
```

Emitted when address `account` unstakes `value` **BRIK** and is charged with `fee`.&#x20;



```
event RewardFetch(uint256 value, uint256 returningFee);
```

Emitted when contract **BRIK** issues an amount `value` of **BRIK** as well as **BRIKI** into the pool. The fee collected from unstaking is also rewarded to the stakeholders.



### Functions

```
function name() external view returns (string memory);
```

Returns the name of the token. Which is `Briky Stake`.



```
function symbol() external view returns (string memory);
```

Returns the symbol of the token. Which is `BRIKI`.



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

Returns the value of **BRIKI** owned by `account`, which is equal to the amount of **BRIK** possessed but sealed in the pool.



```
function transfer(address to, uint256 value) external returns (bool);
```

Moves a `value` amount of **BRIKI** from the caller's account to address `to`, which also presents an equivalent capability of unstaking to retrieve **BRIK**. Returns a boolean value indicating whether the operation succeeded. Emits event `Transfer`.



```
function transferFrom(address from, address to, uint256 value) external returns (bool);
```

Moves a `value` amount of **BRIKI** from `from` to address `to` using the allowance mechanism. `value` is then deducted from the caller's allowance. Returns a boolean value indicating whether the operation succeeded. Emits event `Transfer`.



```
function allowance(address owner, address spender) external view returns (uint256)
```

Returns the remaining number of **BRIKI** that `spender` will be allowed to spend on behalf of `owner` through `transferFrom`. The allowance doesn't increase together with the balance.



```
function approve(address spender, uint256 value) external returns (bool);
```

Sets a `value` amount of **BRIKI** as the allowance of `spender` over the caller's tokens. Emits event `Approval`.



```
function stake(uint256 value) external;
```

Stake a `value` amount of **BRIK** to receive a `value` amount of **BRIKI**. Emits event `Stake`. Should `approve` for contract **BRIKI** such `value` before stake.



```
function unstake(uint256 value) external returns (uint256);
```

Unstake a `value` amount of **BRIKI** to receive **BRIK.** Returns the amount of **BRIK** after being charged for a fee. This fee will be shared among the remainers in the pool.  Emits event `Unstake`.



```
function fetchReward(uint256 value) external;
```

Only **BRIK** contract can call this function and must call this function daily to issue `value` amount of **BRIK** into the pool. The total supply of **BRIKI** is also increased a `value` amount. Emits event `RewardFetch`.

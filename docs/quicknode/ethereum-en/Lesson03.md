# DeFi Course Lesson 3 - Building the Exchange Contract
Updated on Apr 30, 2024

## Overview[](https://www.quicknode.com/courses/ethereum/defi/building-the-exchange-contract#overview "Direct link to Overview")

Welcome to lesson 3 of QuickNode's DeFi DEX course! After completing the Factory contract in [Lesson 2](http://quicknode.com/courses/ethereum/defi/building-a-factory-contract), we're moving on to the second essential component of our DEX platform, the **Exchange** contract. This smart contract will serve as the heart for facilitating swaps and liquidity reserves.

## Introduction to the Exchange Contract[](https://www.quicknode.com/courses/ethereum/defi/building-the-exchange-contract#introduction-to-the-exchange-contract "Direct link to Introduction to the Exchange Contract")

Remember, while the **Factory** contract is responsible for creating the Exchange contract, the **Exchange** contract is where the trading actually takes place. Each unique token pair will be made up of an ETH-ERC20 contract (e.g., DAI/ETH) that will contain both reserves of ETH and an ERC-20 token (meaning traders can swap between the two). This means that there will be separate exchange contracts for, say, DAI/ETH and USDC/ETH, each with its own separate liquidity pool. Adding liquidity to an exchange contract will, in return, provide (mint) you ERC-20 tokens (LP Tokens) that represent your share of the pool. Burning these LP tokens (aka withdrawing from the pool) will, in return, provide you with a proportional share of reserves in the pool, plus any swap fees you earned while providing liquidity.

Now that we know the high-level functionality of the Exchange contract, let's dive deeper into how the Exchange contract is composed and the different concepts to keep in mind:

#### Liquidity Pool[](https://www.quicknode.com/courses/ethereum/defi/building-the-exchange-contract#liquidity-pool "Direct link to Liquidity Pool")

Anyone can become a liquidity provider by depositing an equivalent value of two tokens into the pool. In return, they get "LP tokens" (i.e., ERC-2O tokens) representing their share of the pool. The benefit for liquidity providers is that they earn fees from trades that pass through the pool. However, they are also exposed to something called "impermanent loss," a risk where providing liquidity can be less profitable than just holding onto the tokens (HODLing). Impermanent loss refers to a decrease in the liquidity tokens price (due to fluctuations in the pair's prices). This loss is only realized when you withdraw your liquidity from the pool.

#### Fees[](https://www.quicknode.com/courses/ethereum/defi/building-the-exchange-contract#fees "Direct link to Fees")

In Uniswap V1, a 0.3% fee is charged on every trade. This fee is added to the pool, effectively distributing it to liquidity providers. Over time, as trades happen, these small fees accrue, and when a liquidity provider decides to exit (withdraw), they can claim their portion of the total fees earned while providing liquidity. Fees also ensure that the total pool size increases with every trade.

#### Slippage[](https://www.quicknode.com/courses/ethereum/defi/building-the-exchange-contract#slippage "Direct link to Slippage")

Due to the hyperbolic nature of the pricing curve, larger trades (relative to the pool size) can result in significant price changes. This price movement is called slippage (aka premium). Users can set a maximum slippage they're willing to tolerate; if the trade would result in slippage exceeding this amount, the trade won't execute.

While we're on the topic of functionality, its important to note that Uniswap V1 does not have a lot of the same features as Uniswap V3 or V2. The major upgrade in V2 was the ability to pair ERC-20 tokens against each other (compared to ETH in V1). Meanwhile, in V3, concentrated liquidity was introduced, where users can provide liquidity in different proportions to concentrate liquidity in a certain price range (effectively being more capital efficient).

#### Price Determination[](https://www.quicknode.com/courses/ethereum/defi/building-the-exchange-contract#price-determination "Direct link to Price Determination")

Since Uniswap V1 is an automated market maker (AMM) protocol that facilitates decentralized token swaps, its innovation lies in using a simple mathematical formula called the [Constant Product Market Maker Model](https://en.wikipedia.org/wiki/Constant_function_market_maker) to automatically determine prices without requiring an order book and actual "market makers". The original whitepaper explaining this can be found [here](https://github.com/runtimeverification/verified-smart-contracts/blob/uniswap/uniswap/x-y-k.pdf).

The price of an asset within a liquidity pool in Uniswap V1 is essentially determined by the constant product formula: **x\*y=k**. Where `x` and `y` are two different underlying asset quantities (e.g., DAI and ETH) and `k` is a constant. Asset prices can be represented along a price curve as shown below:

![Price Calculation Graph](https://www.quicknode.com/courses/assets/images/pricing-f7bd2d5879a430a2b0197c1ae7589d3d.png)

For example, if you wish to purchase Asset `B` with Asset `A,` the more `y` (i.e., the quantity of tokens) you want, the more expensive each incremental unit becomes. This property results in increasing prices as larger amounts are swapped, resulting in _slippage_. Slippage is when the quoted price of an asset changes from the time its quoted to you to when it is confirmed on the blockchain, or when a large trade relative to the liquidity pool size occurs. When trading, the amount of tokens you receive is inversely proportional to the amount left in the pool. Due to the constant product formula, buying a token will increase its price, while selling a token will decrease its price. To calculate how much your swap will return, you can rearrange the constant product formula (which we'll cover soon), factoring in the current reserves and the trade size.

In the next section, we'll dive more into the math behind providing liquidity and calculating new implied token prices based on a swap amount. Now let us cover the math briefly.

## Understanding the Math[](https://www.quicknode.com/courses/ethereum/defi/building-the-exchange-contract#understanding-the-math "Direct link to Understanding the Math")

#### Constant Product Market Maker Model[](https://www.quicknode.com/courses/ethereum/defi/building-the-exchange-contract#constant-product-market-maker-model "Direct link to Constant Product Market Maker Model")

To explain the math behind swaps in our DEX, let us break it down with a simplified example of the constant product formula (**x\*y=k**).

**Initial State of the Pool**

Let's say we have a pool with 1,000 ETH and 1,000,000 DAI. Initially, (x = 1,000) (ETH), (y = 1,000,000) (DAI), making (k = 1,000 times 1,000,000 = 1,000,000,000).

**Trading in the Pool**

When a trader wants to buy 5 ETH from the pool, they are effectively reducing the amount of ETH in the pool (x) and increasing the amount of DAI (y). To ensure (k) remains constant after the trade, the equation needs to balance by adjusting (y).

  

-   Before the trade: (x (ETH) = 1,000), (y (DAI) = 1,000,000), (k = 1,000,000,000).
-   With a swap amount of 5 ETH, (x) becomes (995) (because (1,000 - 5 = 995)).
-   To find the new (y) (let's call it (y')), we rearrange the formula to (y' = k/x).
-   Substituting the known values: (y' = 1,000,000,000/995 = 1,005,025.13).

This equates to a new DAI liquidity of 1,005,025.13 in the pool based on a swap amount of 5 ETH. Now let's figure out the new implied prices of each asset in the pool.

**Impact on Prices**

The swap above changes the ratio between ETH and DAI in the pool, thereby adjusting the price of ETH relative to DAI. After the trade, because there are fewer ETH and more DAI in the pool, the price of ETH in terms of DAI increases slightly.

We can verify this by subtracting the new DAI liquidity (e.g., 1,005,025.13) from the previous liquidity (e.g., 1,000,000), resulting in $5025.13 to swap 5 ETH. The implied price of ETH would be 5025.13 / 5 = $1005.027 per ETH. As we discussed previously in the pricing bond curve, a swap to buy more of an asset in a pool would result in the asset price increasing proportionally to the reserves and asset price at the time.

Two important things not factored into the equation above are slippage (premium) and swap fees. However, it is important to realize that a large trade relative to the pool size will incur some slippage, and the trader will pay a premium. The Excel spreadsheet below exhibits some of these costs.

## Determining Prices: Hands-On Exercise[](https://www.quicknode.com/courses/ethereum/defi/building-the-exchange-contract#determining-prices-hands-on-exercise "Direct link to Determining Prices: Hands-On Exercise")

Here is an spreadsheet to help you calculate swap prices based on exchange prices and reserves. Download the spreadsheet locally, enter the swap details and liquidity values, and it will show you the amounts before and after the swap.

|Swap Amount|ETH Liquidity (x)|DAI Liquidity (y)|Constant (k)|Premium|Swap Fee (0.30%)|Cost per ETH|Cost in DAI|
|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|
|-|1,000.00|1,000,000.00|1,000,000,000.00|-|-|$1,000.00|$1,000,000.00|
|1|999.00|1,001,001.00|1,000,000,000.00|0.10|$3.00|$1,001.00|$1,001.00|
|2|998.00|1,002,004.01|1,000,000,000.00|0.20|$6.01|$1,002.00|$2,004.01|
|3|997.00|1,003,009.03|1,000,000,000.00|0.30|$9.03|$1,003.01|$3,009.03|
|4|996.00|1,004,016.06|1,000,000,000.00|0.40|$12.05|$1,004.02|$4,016.06|
|5|995.00|1,005,025.13|1,000,000,000.00|0.50|$15.08|$1,005.03|$5,025.13|

  

## Building the Exchange Contract[](https://www.quicknode.com/courses/ethereum/defi/building-the-exchange-contract#building-the-exchange-contract "Direct link to Building the Exchange Contract")

With the core concepts of the Exchange contract explained, let's get hands-on with some code. Like the Factory contract example we built last lesson, we will guide you through translating the original Vyper code into Solidity. You can view the original Vyper code [here](https://github.com/Uniswap/v1-contracts/blob/master/contracts/uniswap_exchange.vy).

Open the **Exchange.sol** file located within the **src** folder in **defi\_dex**, and let's go to coding! Since this lesson's code is lengthier than last lesson's, we'll break up the code into sections and explain it along the way (the full code is at the end).

### Pragmas and Imports[](https://www.quicknode.com/courses/ethereum/defi/building-the-exchange-contract#pragmas-and-imports "Direct link to Pragmas and Imports")

Copy the code below into your **Exchange.sol** file, then include the remaining code snippets below within the Exchange contract.

```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.13;

import './ERC20.sol';
import "@openzeppelin/contracts/utils/ReentrancyGuard.sol";


contract Exchange is ERC20, ReentrancyGuard {

}
```

We're using the same compiler version as before. Next, we're importing the ERC20 token, along with OpenZeppelin's ReentrancyGuard (which we'll use for security measures). Add the code snippet below to your Exchange contract.

### State Variables[](https://www.quicknode.com/courses/ethereum/defi/building-the-exchange-contract#state-variables "Direct link to State Variables")

```solidity
// Variables
address immutable tokenAddress; // The address of the ERC20 token (being traded)
address immutable factoryAddress; // The interface for the factory contract
```

Then, under the state variables, let's add in the events below.

```solidity
// Events
event LiquidityAdded(address indexed provider, uint ethAmount, uint tokenAmount); // Emitted when liquidity is added
event LiquidityRemoved(address indexed provider, uint ethAmount, uint tokenAmount); // Emitted when liquidity is removed
event TokenPurchased(address indexed buyer, uint ethAmount, uint tokensReceived); // Emitted when a token (ERC-20) is purchased with ETH
event TokenSold(address indexed seller, uint tokensSold, uint ethReceived); // Emitted when ETH is purchased with a token
```

### Functions[](https://www.quicknode.com/courses/ethereum/defi/building-the-exchange-contract#functions "Direct link to Functions")

This contract is more complex than the previous Factory contract. Since our Exchange contract has managed both liquidity and swap functionality.

### Liquidity Functions[](https://www.quicknode.com/courses/ethereum/defi/building-the-exchange-contract#liquidity-functions "Direct link to Liquidity Functions")

Add the liquidity functions into your Exchange contract under the events code.

```solidity
// Liquidity Functions
function addLiquidity(uint tokensAdded) external payable nonReentrant returns (uint256) {
    require(msg.value > 0 && tokensAdded > 0, "Invalid values provided");

    uint ethBalance = address(this).balance;
    uint tokenBalance = getTokenReserves();

    if(tokenBalance == 0) {
        require(IERC20(tokenAddress).balanceOf(msg.sender) >= tokensAdded, "Insufficient token balance");
        IERC20(tokenAddress).transferFrom(msg.sender, address(this), tokensAdded);
        uint liquidity = ethBalance;
        _mint(msg.sender, liquidity);
        emit LiquidityAdded(msg.sender, msg.value, tokensAdded);
        return liquidity;
    } else {
        uint liquidity = (msg.value * totalSupply()) / (ethBalance - msg.value);
        require(IERC20(tokenAddress).balanceOf(msg.sender) >= tokensAdded, "Insufficient token balance");
        IERC20(tokenAddress).transferFrom(msg.sender, address(this), tokensAdded);
        _mint(msg.sender, liquidity);
        emit LiquidityAdded(msg.sender, msg.value, tokensAdded);
        return liquidity;
    }
}

function removeLiquidity(uint256 tokenAmount) external nonReentrant returns(uint, uint) {
    require(tokenAmount > 0, "Invalid token amount");

    uint ethAmount = (address(this).balance * tokenAmount) / totalSupply();
    uint tokenAmt = (getTokenReserves() * tokenAmount) / totalSupply();

    require((getTokenReserves() / address(this).balance) == ((getTokenReserves() + tokenAmt) / (address(this).balance + ethAmount)), "Invariant check failed");
    _burn(msg.sender, tokenAmount);

    payable(msg.sender).transfer(ethAmount);
    IERC20(tokenAddress).transfer
    (msg.sender, tokenAmt);

    emit LiquidityRemoved(msg.sender, ethAmount, tokenAmt);

    return (ethAmount, tokenAmt);
}
```

The liquidity functions (`addLiquidity` and `removeLiquidity`) do the following:

  

-   `addLiquidity`: Allows a user to add liquidity to the exchange by depositing ETH and tokens. The user specifies minimum liquidity, maximum tokens, and a deadline as input parameters. The function first checks if the exchange already has liquidity. If so, it calculates the token amount and liquidity to be minted based on the existing reserves and adds the minted liquidity to the sender's balance. If no liquidity exists, the function sets the initial liquidity equal to the balance of the contract. The function emits events for adding liquidity and for the transfer of the minted liquidity.
-   `removeLiquidity`: Allows a user to remove liquidity from the exchange by specifying an amount of liquidity to remove, minimum ETH, minimum tokens, and a deadline. The function calculates the amount of ETH and tokens to be returned based on the total liquidity and the user's contribution. It updates the sender's balance and the total supply of liquidity. The function then transfers the ETH and tokens back to the user and emits events for removing liquidity and for the transfer of the removed liquidity.

Next, let's get into the swap functionality of the Exchange contract.

### Swap Functions[](https://www.quicknode.com/courses/ethereum/defi/building-the-exchange-contract#swap-functions "Direct link to Swap Functions")

Add the swap functions below under the liquidity logic in your Exchange contract.

```solidity
// Swap Functions
function swapEthForTokens(uint minTokens, address recipient) external payable nonReentrant returns (uint) {
    uint tokenAmount = getTokenAmount(msg.value);
    require(tokenAmount >= minTokens, "Token amount less than expected");

    IERC20(tokenAddress).transfer(recipient, tokenAmount);
    emit TokenPurchased(msg.sender, msg.value, tokenAmount);

    return tokenAmount;
}

function tokenForEthSwap(uint tokensSold, uint minEth) external nonReentrant returns(uint) {
    uint ethAmount = getEthAmount(tokensSold);
    require(ethAmount >= minEth, "ETH amount less than expected");

    IERC20(tokenAddress).transferFrom(msg.sender, address(this), tokensSold);
    payable(msg.sender).transfer(ethAmount);
    emit TokenSold(msg.sender, tokensSold, ethAmount);

    return ethAmount;
}
```

Let's recap some of the code logic above.

-   `swapEthForTokens`: This function allows users to exchange Ethereum (ETH) for tokens. The user sends a specified amount of ETH and receives a calculated number of tokens in return. The number of tokens received must be greater than or equal to minTokens, otherwise, the function will throw an error. The purchased tokens are then transferred to the recipient.
-   `tokenForEthSwap`: This function allows users to exchange tokens for Ethereum (ETH). The user specifies the amount of tokens they want to sell, and in return, they receive a calculated amount of ETH. The ETH amount must be greater than or equal to minEth, otherwise, the function will throw an error. The sold tokens are transferred from the user's address to the contract's address.

### Pricing Functions[](https://www.quicknode.com/courses/ethereum/defi/building-the-exchange-contract#pricing-functions "Direct link to Pricing Functions")

A crucial component to the swap functions are the pricing functions. These allow traders to calculate the token/ETH amounts based on the quantity being purchased/sold. Add the functions in the code snippet below under the swap functions.

```solidity
// Pricing Functions
function getTokenAmount(uint ethSold) public view returns (uint256) {
    require(ethSold > 0, "ETH sold must be greater than 0");
    uint outputReserve = getTokenReserves();
    return getAmount(ethSold, address(this).balance - ethSold, outputReserve);
}

function getEthAmount(uint tokensSold) public view returns (uint256) {
    require(tokensSold > 0, "Tokens sold must be greater than 0"); 
    uint inputReserve = getTokenReserves();
    return getAmount(tokensSold, inputReserve - tokensSold, address(this).balance);
}

function getAmount(uint inputAmount, uint inputReserve, uint outputReserve) public pure returns (uint256) {
    require(inputReserve > 0 && inputAmount > 0, "Invalid values provided");
    uint256 inputAmountWithFee = inputAmount * 997;
    uint256 numerator = inputAmountWithFee * outputReserve;
    uint256 denominator = (inputReserve * 1000) + inputAmountWithFee;
    return numerator / denominator;
}
```

Let's recap the code.

-   `getTokenAmount`: Determines the number of tokens a user would receive for a specified amount of ETH. It first checks that the amount of ETH being sold is greater than 0. Then, it calculates the corresponding token amount based on the reserves of both ETH and the token.
-   `getEthAmount`: Determines the amount of ETH a user would receive for a specified number of tokens. It first checks that the number of tokens being sold is greater than 0. Then, it calculates the corresponding ETH amount based on the reserves of both the token and ETH.
-   `getAmount`: A helper function used by both getTokenAmount and getEthAmount. It performs the core calculation based on the provided input amount, input reserve, and output reserve. The formula accounts for a fee (in this case, 0.3%), which is reflected in the multiplication by 997 and division by 1000.

These functions serve as the public interface for users to query the prices for token-to-ETH and ETH-to-token conversions, both in terms of specified input amounts and desired output amounts.

Lastly, let's add in some additional getter functions that will return the LP token reserves. Add the functions below into your code under the pricing functions.

**Contract Functions**

```solidity
// Contract Functions
function getTokenReserves() public view returns (uint256) {
    return IERC20(tokenAddress).balanceOf(address(this));
}
```

  

-   `getTokenReserves` Returns the token reserves of the LP token associated with the exchange contract

### Full Exchange Contract Code[](https://www.quicknode.com/courses/ethereum/defi/building-the-exchange-contract#full-exchange-contract-code "Direct link to Full Exchange Contract Code")

The full code of the **Exchange.sol** contract should look like this:

```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.13;

import './ERC20.sol';
import "@openzeppelin/contracts/utils/ReentrancyGuard.sol";


contract Exchange is ERC20, ReentrancyGuard {
    //Variables
    address immutable tokenAddress;
    address immutable factoryAddress;

    // Events
    event LiquidityAdded(address indexed provider, uint ethAmount, uint tokenAmount);
    event LiquidityRemoved(address indexed provider, uint ethAmount, uint tokenAmount);
    event TokenPurchased(address indexed buyer, uint ethAmount, uint tokensReceived);
    event TokenSold(address indexed seller, uint tokensSold, uint ethReceived);

    constructor(address _tokenAddress) ERC20("UNI-V1", "UNI-V1") {
        require(_tokenAddress != address(0), "Token address cannot be 0x0");
        tokenAddress = _tokenAddress;
        factoryAddress = msg.sender;
    }

    // Contract Functions
    function getTokenReserves() public view returns (uint256) {
        return IERC20(tokenAddress).balanceOf(address(this));
    }

    // Pricing Functions
    function getTokenAmount(uint ethSold) public view returns (uint256) {
        require(ethSold > 0, "ETH sold must be greater than 0");
        uint outputReserve = getTokenReserves();
        return getAmount(ethSold, address(this).balance - ethSold, outputReserve);
    }

    function getEthAmount(uint tokensSold) public view returns (uint256) {
        require(tokensSold > 0, "Tokens sold must be greater than 0"); 
        uint inputReserve = getTokenReserves();
        return getAmount(tokensSold, inputReserve - tokensSold, address(this).balance);
    }

    function getAmount(uint inputAmount, uint inputReserve, uint outputReserve) public pure returns (uint256) {
        require(inputReserve > 0 && inputAmount > 0, "Invalid values provided");
        uint256 inputAmountWithFee = inputAmount * 997;
        uint256 numerator = inputAmountWithFee * outputReserve;
        uint256 denominator = (inputReserve * 1000) + inputAmountWithFee;
        return numerator / denominator;
    }

    // Liquidity Functions
    function addLiquidity(uint tokensAdded) external payable nonReentrant returns (uint256) {
        require(msg.value > 0 && tokensAdded > 0, "Invalid values provided");

        uint ethBalance = address(this).balance;
        uint tokenBalance = getTokenReserves();

        if(tokenBalance == 0) {
            require(IERC20(tokenAddress).balanceOf(msg.sender) >= tokensAdded, "Insufficient token balance");
            IERC20(tokenAddress).transferFrom(msg.sender, address(this), tokensAdded);
            uint liquidity = ethBalance;
            _mint(msg.sender, liquidity);
            emit LiquidityAdded(msg.sender, msg.value, tokensAdded);
            return liquidity;
        } else {
            uint liquidity = (msg.value * totalSupply()) / (ethBalance - msg.value);
            require(IERC20(tokenAddress).balanceOf(msg.sender) >= tokensAdded, "Insufficient token balance");
            IERC20(tokenAddress).transferFrom(msg.sender, address(this), tokensAdded);
            _mint(msg.sender, liquidity);
            emit LiquidityAdded(msg.sender, msg.value, tokensAdded);
            return liquidity;
        }
    }
    

    function removeLiquidity(uint256 tokenAmount) external nonReentrant returns(uint, uint) {
        require(tokenAmount > 0, "Invalid token amount");

        uint ethAmount = (address(this).balance * tokenAmount) / totalSupply();
        uint tokenAmt = (getTokenReserves() * tokenAmount) / totalSupply();

        require((getTokenReserves() / address(this).balance) == ((getTokenReserves() + tokenAmt) / (address(this).balance + ethAmount)), "Invariant check failed");
        _burn(msg.sender, tokenAmount);

        payable(msg.sender).transfer(ethAmount);
        IERC20(tokenAddress).transfer
        (msg.sender, tokenAmt);

        emit LiquidityRemoved(msg.sender, ethAmount, tokenAmt);

        return (ethAmount, tokenAmt);
    }

    // Swap Functions
    function swapEthForTokens(uint minTokens, address recipient) external payable nonReentrant returns (uint) {
        uint tokenAmount = getTokenAmount(msg.value);
        require(tokenAmount >= minTokens, "Token amount less than expected");

        IERC20(tokenAddress).transfer(recipient, tokenAmount);
        emit TokenPurchased(msg.sender, msg.value, tokenAmount);

        return tokenAmount;
    }

    function tokenForEthSwap(uint tokensSold, uint minEth) external nonReentrant returns(uint) {
        uint ethAmount = getEthAmount(tokensSold);
        require(ethAmount >= minEth, "ETH amount less than expected");

        IERC20(tokenAddress).transferFrom(msg.sender, address(this), tokensSold);
        payable(msg.sender).transfer(ethAmount);
        emit TokenSold(msg.sender, tokensSold, ethAmount);

        return ethAmount;
    }

}
```

We have now completed the construction of the Exchange contract (which is the very core of our DEX), where all trading takes place. With this, our DEX platform is nearly operational! In the next set of lessons, we will focus on integrating these contracts, deploying them, and building a user interface for easy interactions.

# DeFi Course Lesson 1 - Introduction to DeFi
Updated on
Apr 30, 2024

## Overview
In this first lesson of this 7-part DeFi course on building a Decentralized Exchange (DEX), we embark on a journey to explore the dynamic world of Decentralized Finance (DeFi). We will learn about DeFi, including its advantages, risks, use-cases, and then finally cover the most popular protocols in DeFi today. Let's get started!

## What is DeFi?
Decentralized Finance (DeFi) is a blockchain-based financial ecosystem that leverages smart contracts to create an open and permissionless platform for various financial services. Unlike traditional financial systems, DeFi operates without intermediaries, putting users in direct control of their assets and transactions. By removing these intermediaries, DeFi aims to democratize access to financial products and services, providing individuals with more control over their assets.

## Advantages of DeFi
DeFi offers numerous advantages that have attracted a growing community of users and investors:


- **Transparent and Auditable**: All transactions in the DeFi ecosystem are recorded on the blockchain, enabling anyone to verify and audit the operations, fostering trust and accountability.
- **Accessibility**: DeFi platforms are accessible 24/7 to anyone with an internet connection, eliminating geographic barriers and enabling cross-border transactions. Remember that some countries may have regulations on certain types of DeFi activity. For example, the USA currently prohibits overleveraged trading on crypto products (e.g., dYdX perpetual).
- **Innovation and Financial Inclusion**: DeFi allows for rapid experimentation and innovation in financial products and services such as Uniswap (a trading exchange powered by blockchain), Aave (lending powered by blockchain), and more.
- **Interoperability**: DeFi protocols can be interconnected, allowing users to access a wide range of financial services within the ecosystem seamlessly.

## Risks in DeFi
While DeFi presents numerous opportunities, it is essential to be aware of the risks associated with this nascent and rapidly evolving ecosystem:


- **Smart Contract Vulnerabilities and Exploits**: DeFi protocols rely heavily on smart contracts, which, if not correctly audited or secure, can be vulnerable to hacks and exploits, resulting in substantial financial losses. At the time this guide was written, the amount of funds lost due to DeFi exploits was approximately $5.44B (a leaderboard can be found here).
- **Market Volatility and Impermanent Loss**: Users who provide liquidity to DeFi platforms may face impermanent loss, where their assets' value fluctuates relative to holding them in their wallets due to volatile market conditions.
- **Regulatory Challenges and Uncertainties**: The regulatory landscape around DeFi is still evolving, and regulation changes may impact the operations and accessibility of certain DeFi services.
- **User Errors and Security Breaches**: DeFi relies heavily on code that developers write and develop. Users also need to manage their assets properly. Without taking basic preventative measures, you can be susceptible to human errors and phishing attacks, potentially leading to the loss of funds. There is no customer service to save your lost private keys like banks have some password recovery systems.

## DeFi Use Cases
Let's get right into the most common use cases and popular projects in DeFi today.

## Lending and Borrowing
DeFi lending platforms offer a decentralized alternative to traditional banks. Users can lend their digital assets to others and earn interest on their deposits. Borrowers can utilize their digital assets as collateral to secure loans. Most DeFi lending protocols require over-collateralization because there would be too much risk for lenders, as most protocols don't require KYC. The main idea behind DeFi lending is to ensure that borrowers have enough collateral to secure their loans, making it less likely for lenders to suffer losses in case of default.

### Use Case: Aave
Aave is pioneering the way for decentralized lending and borrowing. It operates on the Ethereum blockchain and several other EVM-compatible blockchains (e.g., Arbitrum, Optimism, Polygon, etc.). It enables users to lend their assets to the platform and earn interest or borrow assets using their collateral.

#### How Does Aave Work?

- **Lending Pools**: Aave operates with separate lending pools for each supported asset. Users can deposit their assets into these pools and earn interest based on the supply and demand dynamics. The interest rates paid to suppliers that borrowers pay are based on the relationship between the liquidity available in the protocol, the demand an asset has, and its supply/demand dynamics.
- **Borrowing**: Borrowers can access funds from Aave by providing collateral to the protocol. The amount of assets they can borrow depends on the collateral value provided and its collateralization ratio.
- **Variable and Stable Interest Rates**: Aave offers both variable and stable interest rates. The utilization rate of each pool determines the variable rate, while the stable rate is based on the protocol's strategy and market conditions.
- **Flash Loans**: Aave introduced the concept of "Flash Loans," enabling users to borrow assets without requiring any collateral as long as the borrowed amount is returned within the same transaction. This allows entities to find arbitrage between cryptocurrency prices and try to profit from them without requiring any capital (excluding gas fees).

## Trading
One of the biggest use cases in DeFi is trading. This is usually done through decentralized exchanges (DEXs) like Uniswap, which enable peer-to-peer trading of cryptocurrencies without requiring a central authority. Users retain control of their assets by trading directly from their wallets, reducing counterparty risk and promoting privacy. Uniswap is one of the biggest DEXs to date that facilitates billions of dollars of trading activity.

### Use Case: Uniswap
Uniswap is a leading decentralized exchange (DEX) protocol built on the Ethereum blockchain. It allows users to swap various ERC-20 tokens and ETH directly from their wallets. Uniswap employs an automated market maker (AMM) model for providing liquidity and calculating token prices. This model is similar to how a market maker in the traditional financial markets provides liquidity to the market. Uniswap aims to achieve the same but at an automated level.

**Automated Market Maker (AMM)**: AMMs are a type of trading model where traders can swap cryptocurrencies without needing a traditional order book. Instead, they rely on smart contracts to automatically calculate trade prices based on a predetermined algorithm and the ratio of tokens in a liquidity pool. AMMs do not require buyers and sellers to be matched directly; this makes them more efficient than traditional order book exchanges. However, AMMs can also have higher slippage. Slippage is the difference between the expected price of a trade and the actual price at which it is executed, depending on the liquidity pool depth.

In short, AMMs use algorithms and liquidity pools to determine trade prices automatically, while order book systems in traditional financial markets rely on buy and sell orders to facilitate trades like traditional markets.

Uniswap (V1) had limited functionality in its early days, then continued development and launched Uniswap V2, and now Uniswap V3 is in use. Overall, Uniswap V3 is a significant improvement over previous versions. It offers more capital efficiency for liquidity providers, resulting in the potential for higher rewards for liquidity providers and lower slippage for traders.

## Staking
Staking is a process in which users lock up their ETH for a period of time to support the blockchain network in validating transactions, forming blocks and earning rewards in doing so. The act of Staking on Ethereum is called being a Validator, which includes running software and staking ETH.

To become a validator, a user will need a stable internet connection, a computer with sufficient processing power, memory, storage, and 32 ETH (this is a requirement). However, luckily, there are other options for staking on the Ethereum blockchain, which does not require 32 ETH. These options (staking pools and liquid staking) are highlighted below:


- **Validators**: Validators are entities responsible for validating blocks and maintaining the network. To become a validator, you must deposit 32 ETH and run the Ethereum consensus and execution client software. Validators should always be online and responsive, and they are subject to slashing penalties if they are not responsive or try bad actor practices.
- **Staking Pools**: Staking pools (e.g., RocketPool) allow users to pool their ETH together and delegate their voting power to a validator. This means that users do not need to run their own validator node, and they can still participate in the network's security without having to deposit the total 32 ETH. Staking pools typically charge a fee for their services, but they can offer a variety of features, such as liquid staking and staking rewards.
- **Liquid Staking**: Liquid staking (e.g., Lido) is a process that allows users to stake their ETH and receive a synthetic token in return that receives staking rewards in real-time without having to run the software and hardware needed to become a Validator. Additionally, there are no minimum liquid staking requirements. All this together allows users to retain flexibility while benefiting from staking rewards.

### Use Case: Liquid Staking - Lido
Lido is an innovative protocol that enables liquid staking, providing a solution for token holders who want to participate in staking in proof-of-stake networks like Ethereum without having to run their own hardware or worry about ETH staking limits. This enables users to actively participate in network consensus without the typical lockup periods associated with staking. Users who stake their ETH via Lido receive stETH tokens in return, which represent their initial deposit + staking rewards. stETH is also an ERC-20 token and can be traded and used in various DeFi applications, providing users with flexibility and liquidity while staking.

## Yield Farming
Yield farming involves users providing liquidity to various DeFi protocols in exchange for rewards in the form of additional tokens. This process rewards users for their engagement with a protocol and improves protocol functionality as liquidity rises (e.g., less slippage, better interest rates). Yield farming is most commonly introduced via campaigns (for some fixed time period) and attempts to get users interested (potentially from other projects) and engaged with their token.

### Use Case: Yearn Finance
Yearn Finance is a decentralized yield aggregator that optimizes DeFi returns for users by automatically moving their funds between different lending protocols. By doing so, investors don't need to worry about migrating from project to project to maximize their yield, and instead, Yearn Finance automates this process for them. Yearn Finance accomplishes its goal by introducing "Vaults", which are smart contracts that pool users' funds and automate the yield optimization process.

## Airdrops
Another method web3 projects use to increase engagement and liquidity is via airdrops. An airdrop results in web3 projects sending tokens to users who have engaged with them. The requirements for users getting gifted tokens vary on the project type and its use case. For example, a popular airdrop that occurred on Ethereum was for ENS (Ethereum Domain Service), which sent ENS tokens to users who previously registered an ENS name. With these gifted tokens, users can either hold them and participate in activities like governance or sell them on the open market via a DEX like Uniswap.

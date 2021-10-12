---
description: Price feed oracles on WeFarm
---

# Oracle

### Basics

There are three types of Oracle that WeFarm is currently using to feed the prices for available token assets on WeFarm: **Third-party oracles, DEX Oracle, and **WeFarm** Oracle.**

### **Third-Party Oracles **

Third-party Oracles such as Chainlink, OKLink are used on WeFarm across different blockchains. Chainlink Oracle is currently the most adopted and trusted third-party oracle in DeFi. Chainlink feeds the price to some of the most reputable DeFi projects. That’s why WeFarm uses Chainlink Oracle whenever there is availability on different chains. ( Click [here](https://docs.chain.link) to find more information about Chainlink Oracle.)  For chains where Chainlink is not available, we adopt the most trustworthy oracle of that particular chain. For example, the Chainlink oracle is not available on OKEx Chain and we adopted the official OKLink Oracle as the third-party price feed. \


### **DEX Oracles**

DEX Oracle came into the picture when tokens are not supported by Chainlink Oracle but have sufficient liquidity on one particular decentralized exchange.DEX that we adopted is different on different chains. It depends on the overall liquidity and trading volume. Currently, we use Uniswap as our DEX oracle on the Ethereum blockchain and SushiSwap on OKExChain. The community will review the source and DEX Oracle periodically to ensure WeFarm adopts the most trustworthy and deep liquidity oracle. \


### WeFarm** Oracle **

WeFarm Oracle is used if there is no trustworthy third-party oracle available and the DEX trading volume and liquidity of that particular token are relatively low. The token price of WeFarm oracle is reviewed periodically to ensure the price is within an acceptable bound of the time-weighted average price of the token/USD pair across major trading venues, including both centralized and decentralized exchanges. WeFarm oracle proxy contract only stores prices that are within an acceptable bound of the Time-Weighted Average Price (TWAP) and are updated only when the TWAP deviates from the acceptable bound. If there is a significant breakthrough of the token price, the price feed would be triggered to ensure the new price is within the new acceptable bound. The WeFarm Oracle also contains logic that upscales the posted prices into the format that WeFarm's Comptroller expects.

### Price Feeds Contract Addresses


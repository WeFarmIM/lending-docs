# WeFarm SDK



### Overview

Currently, we are using `web3.js` to interact with our contract on the mainnet. If there are some changes in our contract, we need to change this everywhere in different projects that are developed by different developers, which will cause much time. Currently, our web application, mobile application, liquidator bot, and even in the tests are all adopting this approach. We will save a lot of time if we build an SDK that can be used in all these files it can decouple the components more and shorten time to coordinate when upgrading our contracts.

Examples:

1. Compound: [https://github.com/compound-finance/compound-js](https://github.com/compound-finance/compound-js)
2. DyDx: [https://github.com/dydxprotocol/solo](https://github.com/dydxprotocol/solo)

### Goal

#### Example

We need to use it to interact with the contracts just by providing the provider information. The potential usage can be like the following:

1. Download the package using npm

```
npm i -s @WeFarm/wefarm-protocol
```



* Download the package using npm

```
yarn add @WeFarm/wefarm-protocol
```

2\. Initialize the WeFarm Instance

```javascript
import WeFarm from "@WeFarm/wefarm-protocol"

var wefarm = new WeFarm(window.ethereum); // web browser

var wefarm = new WeFarm('http://127.0.0.1:8545'); // HTTP provider

var wefarm = new WeFarm(); // Uses Ethers.js fallback mainnet (for testing only)

var wefarm = new WeFarm('ropsten'); // Uses Ethers.js fallback (for testing only)

// Init with private key (server side)
var wefarm = new WeFarm('https://mainnet.infura.io/v3/_your_project_id_', {
  privateKey: '0x_your_private_key_', // preferably with environment variable
});

// Init with HD mnemonic (server side)
var wefarm = new WeFarm('mainnet', {
  mnemonic: 'cyber punk game...', // preferably with environment variable
});
```

3.Use the instance to send transactions:

```javascript
console.log(WeFarm.address.DAI, WeFarm.address.cETH);  // get the addresses used by the current compound

await wefarm.borrow(WeFarm.addresses.DAI, new BN(10).pow(new BN(18))) // Borrow one whole DAI from WeFarm
```

4\. Interact with the backend API

```javascript
// Get one account's LTV
const accLtv = await wefarm.API.ltv(address)
```

### Modules

WeFarm

This is the main object of this SDK. Users should provide a provider to create a WeFarm object then use this instance to interact with the contracts. After the provider is set, we can specify one account that is available in the provider to interact with the contract. This should be a singleton. By default, it will use the first account in your provider to conduct all the transactions.

#### ContractInstance

By specifying the contract's address and ABI, this should return the corresponding contract instance.

#### ContractInstanceStore

We can use this object to get all the different contract instances. We make sure each contract only has one instance throughout the same app to ensure performance. 

#### AccountsInstance

This is the wrapper class of the Accounts contract, we can use this to interact with the Accounts contract on the chain.

#### SavAccInstance

This is the wrapper class of the SavingAccount contract, we can use this to interact with the SavingAccount contract on the chain.

#### BankInstance

This is the wrapper class of the Bank contract, we can use this to interact with the Bank contract on the chain.

#### Constants

This file contains all the constants that will be used by the SDK. Like the addresses of our protocols on different test nets and also the mainnet, and the newest ABIs of each different contract.

#### Utils

Some utility functions that can help to get some necessary data from the third-party source, as the real-time gas price.

### API

#### Contract Related

* wefarm.userHasAnyDeposits(user: string, target: string): 
  * Description: Return whether the target account has deposit or not.
  * Parameters: 
    * user: The address of the user who tries to call this function.
    * target: The address of the target that the user wants to query.
* wefarm.getDepositPrincipal(tokenName: string, user: string, target: string): 
  * Description: Return the account's deposit balance in WeFarm for a specific token.
  * Parameters:
    * tokenName: The token's name that you want to query, please check what token names are supported in the **Token Names** section.
    * user: The address of the user who tries to call this function.
    * target: The address of the target that the user wants to query.
* wefarm.getBorrowPrincipal(tokenName: string, user: string, target: string): 
  * Description: Return the account's borrow balance in WeFarm for a specific token.
  * Parameters:
    * tokenName: See **Token Names** section.
    * user: The address of the user who tries to call this function.
    * target: The target address that the user wants to query.
* wefarm.getLastDepositBlock(tokenName: string, user: string, target: string): 
  * Description: Return the last deposit block of a user for a specific token.
  * Parameters:
    * tokenName: See **Token Names** section.
    * user: The address of the user who tries to call this function.
    * target: The target address that the user wants to query.
* wefarm.getLastBorrowBlock(tokenName: string, user: string, target: string): 
  * Description: Return the last borrow block of a user for a speicific token.
  * Parameters:
    * tokenName: See **Token Names** section.
    * user: The address of the user who tries to call this function.
    * target: The target address that the user wants to query. 
* wefarm.getDepositInterest(tokenName: string, user: string, target: string): 
  * Description: Return the deposit interests since the last checkpoint.
    * Parameters:
      * tokenName: See **Token Names** section.
      * user: The address of the user who tries to call this function.
      * target: The target address that the user wants to query. 
* wefarm.getBorrowInterest(tokenName: string, user: string, target: string):
  * Description: Return the borrow interests since the last checkpoint.
    * Parameters:
      * tokenName: See **Token Names** section.
      * user: The address of the user who tries to call this function.
      * target: The target address that the user wants to query. 
* wefarm.getDepositBalanceCurrent(tokenName: string, user: string, target: string): 
  * Description: Return the total deposit balance of the target address. 
    * Parameters:
      * tokenName: See **Token Names** section.
      * user: The address of the user who tries to call this function.
      * target: The target address that the user wants to query. 
* wefarm.getBorrowBalanceCurrent(tokenName: string, user: string, target: string):
  * Description: Return the total borrow balance of the target user. 
    * Parameters:
      * tokenName: See **Token Names** section.
      * user: The address of the user who tries to call this function.
      * target: The target address that the user wants to query. 
* wefarm.getBorrowPower(user: string, target: string): 
  * Description: Return the borrow power in ETH wei unit.
  * Parameters:
    * user: The address of the user who tries to call this function.
    * target: The target address that the user wants to query.
* wefarm.getDepositETH(user: string, target: string):
  * Description: Return the total deposit value in ETH wei unit.
  * Parameters:
    * user: The address of the user who tries to call this function.
    * target: The target address that the user wants to query.
* wefarm.getBorrowETH(user: string, target: string): 
  * Description: Return the total borrow value in ETH wei unit.
  * Parameters:
    * user: The address of the user who tries to call this function.
    * target: The target address that the user wants to query.
* wefarm.isAccountLiquidatable(user: string, target: string): 
  * Description: Return whether one account is liquidatable or not.
  * Parameters:
    * user: The address of the user who tries to call this function.
    * target: The target address that the user wants to query.
* wefarm.deposit(token: Token, amount: any):
  * Description: Deposit an amount of token to WeFarm. The default account is the first account in the provider.
  * Parameters:
    * token: See **Token Names** section.
    * amount: The amount defined in BigNumber type.
* wefarm.borrow(token: Token, amount: any): Borrow an amount of token from WeFarm.
  * Description: Borrow an amount of token from WeFarm. The default account is the first account in the provider.
    * Parameters:
      * token: See **Token Names** section.
      * amount: The amount defined in BigNumber type.
* wefarm.repay(token: Token, amount: any): Repay an amount of token to WeFarm.
* Description: Repay an amount of token to WeFarm. The default account is the first account in the provider.
  * Parameters:
    * token: See **Token Names** section.
    * amount: The amount defined in BigNumber type.
* wefarm.withdraw(token: Token, amount: any): Withdraw an amount from WeFarm.
  * Description: Borrow an amount of token from WeFarm. The default account is the first account in the provider.
    * Parameters:
      * token: See **Token Names** section.
      * amount: The amount defined in BigNumber type.
* wefarm.withdrawAll(token: Token): Withdraw all tokens of a specific kind from WeFarm for a specific token.
  * Description: Withdraw all tokens from WeFarm. The default account is the first account in the provider.
    * Parameters:
      * token: see **Token Names** section.
      * amount: The amount defined in BigNumber type.
* wefarm.getTotalDepositStore(tokenName:string): 
  * Description: Get the total deposit amount of a specific token.
  * Parameters:
    * tokenName: See **Token Names** section.
* wefarm.getBorrowRatePerBlock(tokenName: string): 
  * Description: Get the borrow rate of the token.
  * Parameters:
    * tokenName: See **Token Names** section.
* wefarm.getDepositRatePerBlock(tokenName:string): 
  * Description: Get the deposit rate of the token.
  * Parameters:
    * tokenName: See **Token Names** section.
* wefarm.getCapitalUtlizationRatio(tokenName: string): 
  * Description: Get the U ratio of the token.
  * Parameters:
    * tokenName: See **Token Names** section.
* wefarm.getCapitalCompoundRatio(tokenName: string): 
  * Description: Get the C ratio of the token.
  * Parameters:
    * tokenName: See **Token Names** section.
* wefarm.getTokenState(tokenName: string): 
  * Description: Get the current token state.
  * Parameters:
    * tokenName: See **Token Names** section.
* wefarm.getPoolAmount(tokenName: string): Get the token still in the contract.
  * Description: Get the number of tokens that are still in the contract
    * Parameters:
      * tokenName: See **Token Names** section.

#### Token Names

* All the supported token names in wefarm-js:
  * BAT
  * DAI
  * ETH
  * REP
  * USDC
  * USDT
  * WBTC
  * ZRX
  * MKR
  * FIN
  * LPToken: The FIN liquidity provider token related to the uniswap.
  * TUSD
* We can use the above names as strings to pass as a parameter, or we can use enum Token type when it requires a Token type parameter.
* The enum Token type is defined as:

```javascript
export enum Token {
    BAT = 1,
    DAI,
    ETH,
    REP,
    USDC,
    USDT,
    WBTC,
    ZRX,
    MKR,
    FIN,
    LPToken,
    TUSD
}
```

#### Stat Related

For more details, please review:https://app.gitbook.com/@wefarm/s/front-end/api-info-about-stat.wefarm.cn

* wefarm.API.statusAssets(): Call `/api_v2/address/status_assets`
* wefarm.API.balances(): Call `/api_v2/address/balances`
* wefarm.API.ltv(): Call `/api_v2/address/ltv`
* wefarm.API.balanceLog(): Call `/api_v2/address/balance_log`
* wefarm.API.getSavingsOrder(): Call `/api_v2/address/get_savings_order`
* wefarm.API.tokenStatus(): Call `/api_v2/market/token_status`
* wefarm.API.tokenStatistical(): Call `/api_v2/market/token_statistical`
* wefarm.API.tokenPrice(): Call `/api_v2/market/token_prices`
* wefarm.API.totalAssets(): Call `/api_v2/market/total_assets`
* wefarm.API.addressList(): Call `/api_v2/market/address_list`

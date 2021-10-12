---
description: Overview of Core Smart Contracts
---

# Overview

### Contracts

We have five central contracts in our protocol, `SavingAccount`, `Accounts`, `Bank`, `TokenRegistry`, and `GlobalConfig`.

The first three contracts are upgradable, and they contain the main business logic of the protocol. The overall structure of the protocol is monolithic, which means that `SavingAccount`, `Accounts`, and `Bank` contracts work closely with each other. We decoupled this into three contracts only for code size limit reasons. The naming here may be a little counterintuitive. Usually, when we want to deposit money to a bank, we first go to the bank and open a savings account, then we deposit money to this account. Here, the `SavingAccount` contract behaves like a traditional bank, which is the entry point for you to deposit or borrow money from WeFarm. The Bank contract records the state of the pool of WeFarm's protocol, like the total deposit, total borrow, and the current rate. The Accounts contract records the deposit and borrow balances for each specific user.

The workflow of a transaction to WeFarm works as the following diagram. The user sends transactions by calling functions on the `SavingAccount` contract, and then the `SavingAccount` contract will call the `Bank` contract to create a rate index checkpoint, which is to compute the accumulated interests for all the users. Then the Bank contract will call the functions in the `Account` contract to calculate the balance change in the user account.

![The workflow of a transaction](<../.gitbook/assets/The workflow of a transaction.png>)

The TokenRegistry and GlobalConfig contracts are to save data, and the three central contracts will use it. Like all three contracts' addresses, so in these three contracts, they only need to keep the global config's address in the contract's memory space.

### SavingAccount

This contract is the interface that our users will mainly interact with. Users will send transactions to this contract to `deposit`, `withdraw`, `borrow`, `repay`,  `withdrawAll`, and `liquidate` functions to interact with our protocol. For each operation, this contract is the contract that receives and sends out the tokens. Whenever users send a transaction to the `SavingAccount` contract, it will emit an event to log this transaction.

#### Borrowing Power

The borrowing power of a user is related to the collateral that the user deposited into our system. The contract obtains the price from Chainlink's oracle. If the LTV of a user is too large, then it is at the risk of being liquidated. Currently, we set the liquidation threshold to around 0.8. 

### Bank

This contract is to create rate indexes whenever there is an interaction with our `SavingAccount` contract. We are using rate indexes to compute the borrow and deposit interests. Given the different indexes, we calculate the user balances here. Whenever the contract creates an index, this contract will emit an event.

#### Rate Index 

i and j here represent two different blocks.

$$RateIndex_i = RateIndex_j * (1 + RatePerBlock_{ij} * (i - j))$$ 

#### Balance

i and j here represent two different blocks.

$$Balance_i = Balance_j \times RateIndex_j \div RateIndex_i$$ 

Borrow balances and deposit balances both use this method to compute.

### Accounts

This contract is to record the balances of each user account for different tokens. This contract also contains a BitMap which will quickly show whether a user has depositings/borrowings for each token while costs less gas.

### GlobalConfig

This contract stores all the contract's addresses, so all other contracts can only keep GlobalConfig's address to call functions of other contracts. It is also used to config the reserve ratio and community fund ratio of the protocol.

### TokenRegistry

This contract records the meta-data about each contract-supported token. We can add new supported tokens using this contract.

#### Current Supported Tokens

1. DAI
   1. Token Address: 0x6b175474e89094c44da98b954eedeac495271d0f
   2. cToken Address: 0x5d3a536e4d6dbd6114cc1ead35777bab948e3643
2. USDC
   1. Token Address: 0xa0b86991c6218b36c1d19d4a2e9eb0ce3606eb48
   2. cToken Address: 0x39aa39c021dfbae8fac545936693ac917d5e7563
3. USDT
   1. Token Address: 0xdac17f958d2ee523a2206206994597c13d831ec7
   2. cToken Address: 0xf650c3d88d12db855b8bf7d11be6c55a4e07dcc9
4. TUSD
   1. Token Address: 0x0000000000085d4780B73119b644AE5ecd22b376
   2. cToken Address: Not Compound supported.

### Upgradability

For `SavingAccount`, `Accounts`, and `Bank` contracts, we are using OpenZeppelin proxies to conduct the upgrading process. So other than the contracts, we also have an OpenZeppelin proxy contract for each of these three central contracts. We can never change the proxy contract addresses, but the contract of the underlying implementation is changeable.

![The workflow of a proxy contract](<../.gitbook/assets/The workflow of a proxy contract.png>)

For the GlobalConfig and TokenRegistry contracts, we can deploy a new contract and call the setter function in three central contracts to point to the new contracts since they don't save any transactional data.

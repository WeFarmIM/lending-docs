---
description: >-
  WeFarm's upgradeable contracts can only be upgraded after 48 hours of
  scheduled upgrade
---

# Delayed Upgrades

WeFarm upgradeable contracts (SavingAccount.sol, Bank.sol, Accounts.sol) are governed by the ProxyAdmin contract. These three contracts can be upgraded to new implementation via ProxyAdmin. From now on this ProxyAdmin contract's ownership is transferred to the TimelockController contract (from OpenZeppelin). Hence, any upgrade of the contract will be done via TimelockController contract. The TimelockController contract also enforces a delayed execution of the scheduled requests.

### TimelockController.sol

The TimelockController contract is developed by OpenZeppelin. This contract has three roles which manages the request and their execution. These three roles are.

1. **TIMELOCK_ADMIN_ROLE**: This role can grant or revoke other two roles from the contract.
2. **PROPOSER_ROLE**: This role can propose and schedule a request to the contract for the delayed execution.
3. **EXECUTOR_ROLE**: This role can execute an already scheduled request after the given delay is passed.

At WeFarm we are managing these roles with different hardware wallets. When in the future we plan to upgrade the contract, we will publish the information to the community, so that they can take the informed decisions. The upgrade will be executed after 48 hours of delay.

### Addresses

#### Mainnet

#### Testnet

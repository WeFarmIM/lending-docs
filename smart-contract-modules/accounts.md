---
description: Accounts contract
---

# Accounts

The `Accounts` contract tracks the principal, interest, and other balance-related status of accounts.

## getBorrowBalanceCurrent

#### function head

`function getBorrowBalanceCurrent (address _token, address _accountAddr) public view returns (uint256 borrowBalance)`

* parameters
  * \_token: The address of token that you want to query for.
  * \_accountAddr: The address of the account that you want to query for.
* return:
  * The current borrow balance of a specific token of one user.

#### description

Get the current borrow balance of a token for an account.

## getDepositBalanceCurrent

#### function head

`function getDepositBalanceCurrent (address _token, address _accountAddr) public view returns (uint256 depositBalance)`

* parameters
  * \_token: The address of token that you want to query for.
  * \_accountAddr: The address of the account that you want to query for.
* return:
  * The current borrow balance of a specific token of one user.

Get the current deposit balance of a token for an account.

## getBorrowETH

#### function head

`function getBorrowETH(address _accountAddr) public view returns (uint256 borrowETH)`

* parameters:
  * \_accountAddr: The address that you want to query for.
* return:
  * The current total value of the borrowed asset of one user in ETH wei unit.

#### description

Get the total amount of borrowed tokens in the value of ETH for an account.

## getDepositETH

#### function head

`function getDepositETH(address _accountAddr) public view returns (uint256 depositETH)`

* parameters:
  * \_accountAddr: The address of the account that you want to query for.

#### description

Get the total amount of deposited tokens in the value of ETH for an account.

## getBorrowPrincipal

#### function head

`function getBorrowPrincipal(address _accountAddr, address _token) public view returns(uint256)`

* parameters:
  * \_accountAddr: The account address that you want to query for.
  * \_token: The address of token that you want to query for.
* return:
  * The borrow principal of the current user.

#### description

Get current borrow principal of a token for an account.

## getDepositPrincipal

#### function head

`function getDepositPrincipal(address _accountAddr, address _token) public view returns(uint256)`

* parameters:
  * \_accountAddr: The account address that you want to query for.
  * \_token: The address of token that you want to query for.
* return:
  * The deposit principal of the current user.

#### description

Get the current deposit principal of a token for an account.

## getBorrowInterest

#### function head

`function getBorrowInterest(address _accountAddr, address _token) public view returns(uint256)`

* parameters:
  * \_accountAddr: The account address that you want to query for.
  * \_token: The address of token that you want to query for.
* return:
  * The borrow interests of the current user.

#### description

Get current borrow interest of a token for an account.

## getDepositInterest

#### function head

`function getDepositInterest(address _account, address _token) public view returns(uint256)`

* parameters:
  * \_account: The account address that you want to query for.
  * \_token: The address of token that you want to query for.
* return:
  * The deposit interests of the current user.

#### description

Get current deposit interest of a token for an account.

## getBorrowPower

#### function head

`function getBorrowPower(address _borrower) public view returns (uint256 power)`

* parameters:
  * \_borrower: The account that you want to query for.
* return:
  * power: The borrowing power computed by deposited assets by this user.

#### description

Get current borrow power of an account. The borrowing power is the sum of the value of collateral tokens discounted by their borrow LTVs.

## getLastBorrowBlock

#### function head

`function getLastBorrowBlock(address _accountAddr, address _token) public view returns(uint256)`

* parameters:
  * \_accountAddr: The account address you want to query for.
  * \_token: The token address you want to query for.
* return:
  * The last recent block that the user has sent a borrow transaction.

#### description

Get the block number where the latest borrow of a token happened for an account.

## getLastDepositBlock

#### function head

`function getLastDepositBlock(address _accountAddr, address _token) public view returns(uint256)`

* parameters:
  * \_accountAddr: The account address you want to query for.
  * \_token: The token address you want to query for.
* return:
  * The last recent block that the user has sent a deposit transaction.

#### description

Get the block number where the latest deposit of a token happened for an account.

## isUserHasAnyDeposits

#### function head

`function isUserHasAnyDeposits(address _account) public view returns (bool)`

* parameters:
  * \_account: The address of the account that you want to query for.
* return:
  * A boolean value to indicate whether a user has deposited any tokens to WeFarm.

#### description

Return true if an account has the positive deposit of any token. Otherwise, return false.

## isUserHasBorrows

#### function head

`function isUserHasBorrows(address _account, uint8 _index) public view returns (bool)`

* parameters:
  * \_account: The address of the account you want to query for.
  * \_index: The index of the token that you saved in the TokenRegistry.
* return:
  * A boolean value to indicate whether a user has borrowed a specific kind of token.

#### description

Return true if an account has a positive borrow balance of a token. Otherwise, return false.

## isUserHasDeposits

#### function head

`function isUserHasDeposits(address _account, uint8 _index) public view returns (bool)`

* parameters:
  * \_account: The address of the account you want to query for.
  * \_index: The index of the token that you saved in the TokenRegistry.
* return:
  * A boolean value to indicate whether a user has deposited a specific kind of token.

#### description

Return true if an account has a positive deposit balance of a token. Otherwise, return false.

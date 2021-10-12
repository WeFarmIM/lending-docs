---
description: SavingAccount contract
---

# SavingAccount

SavingAccount is the primary interface contract that processes a user's deposit/withdraw and borrow/repay requests, the details of these methods are in the `Bank` contract.  It also provides the functionality for the third-parities for liquidation. Please check the modifier page to confirm the semantics of each modifier in the function head here.

## deposit

#### function head

`function deposit(address _token, uint256 _amount) public payable onlySupportedToken(_token) onlyEnabledToken(_token) nonReentrant`

* parameters
  * \_token: The address of the token that the user wants to deposit.
  * amount: The volume of the token that the user wants to deposit.

#### description

The function receives a certain amount of tokens from `msg.sender` and deposits them to the pool. This function calls the `deposit` function in the `Bank` contract and `emit` the`Deposit` event. Please make sure your account has enough tokens to avoid gas waste.

## withdraw

#### function head

`function withdraw(address _token, uint256 _amount) external onlySupportedToken(_token) whenNotPaused nonReentrant`

* parameters
  * \_token: The address of the token that the user wants to withdraw.
  * amount: The volume of the token that the user wants to withdraw.

#### description

The function withdraws a certain amount of tokens from the pool and then `send` them to `msg.sender`. This function calls the `withdraw` function in the `Bank` contract, and `emit` the `Withdraw` event.

You can't withdraw tokens to make your borrow power which is less than the total value of your borrowed tokens. For more info about borrow power, please check the description on the Accounts page.

The actual token an account withdraw could be smaller than it requested because 10% of the interest is deducted and saved as the WeFarm community fund.

## withdrawAll

#### function head

`function withdrawAll(address _token) external onlySupportedToken(_token) whenNotPaused nonReentrant`

* parameters
  * \_token: The address of the token that the user wants to withdraw.

#### description

The function withdraw all tokens of an account from the pool and send them to `msg.sender.` This function first checks the current deposit balance of the account by calling `getDepositBalanceCurrent` in the `Accounts` contract, and then calls the `withdraw` function in the `Bank` contract to request withdrawing that exact amount of token and emit the `WithdrawAll` event.

The actual token an account withdraw could be smaller than its total deposit balance because 10% of the interest is deducted and saved as the WeFarm community fund.

If, after withdrawing all these kinds of tokens, your borrow power will be less than the value of your borrowed tokens, this function will fail. Please check the `Accounts` page for more info about **borrow power**.

## borrow

#### function head

`function borrow(address _token, uint256 _amount) external onlySupportedToken(_token) onlyEnabledToken(_token) whenNotPaused nonReentrant`

* parameters
  * \_token: The address of the token that the user wants to borrow.
  * amount: The volume of the token that the user wants to borrow.

#### description

An account uses `borrow` function to borrow a certain amount of token from the pool. This function calls the `borrow` function in the `Bank` contract, and `emit` the `Borrow` event.

You can't borrow tokens that have value more than your borrow power. Please check `Accounts` page for more info about **borrow power**.

## repay

#### function head

`function repay(address _token, uint256 _amount) public payable onlySupportedToken(_token) nonReentrant`

* parameters
  * \_token: The address of token you want to repay.
  * amount: The amount of token you want to repay.

#### description

An account uses `repay` function to repay a certain amount of borrowed token to the pool. This function calls the `repay` function in the `Bank` contract, and `emit` the `Repay` event.

If an account repaid more than what it owes, the contract would return the extra token to the account address.

In your wallet, you need to make sure there are enough tokens that are larger or equal to the amount you specify here to avoid failing transaction. This amount of tokens should be in your wallet not the already deposited tokens to WeFarm.

## transfer

#### function head

`function transfer(address _to, address _token, uint _amount) external onlySupportedToken(_token) onlyEnabledToken(_token) whenNotPaused nonReentrant`

* parameters
  * \_to: The account address that you want to transfer tokens to.
  * \_token: The address of the tokens you want to transfer.
  * \_amount: The volume of token you want to transfer.

#### description

An account could transfer its deposited token to another account inside WeFarm's saving pool. When a transfer happens, the deposit balance of `msg.sender` is deducted and added to the account specified in the `transfer` function. It means that the transfer will only occur within the WeFarm's Protocol, and the `_to` address won't see the transferred tokens in its wallet, but it will show in WeFarm's balance.

If the `msg.sender` has borrowed any tokens from the pool, it requires that the account is still under the borrowing capacity after transfer.

## liquidate

#### function head

`function liquidate(address _borrower, address _borrowedToken, address _collateralToken) public onlySupportedToken(_borrowedToken) onlySupportedToken(_collateralToken) whenNotPaused nonReentrant`

* parameters
  * \_borrower: The target account to liquidate through this transaction.
  * \_borrowedToken: The token that the borrower has borrowed.
  * \_collateralToken: The underlying collateral token that the liquidator wants to buy.

#### description

A borrower (or debtor) is liquidatable if its LTV is above 85%. If an account is liquidatable, a liquidator can use a debt token to purchase the collateral from the borrower with a 5% discount.

For a full explanation about the liquidate function, please visit [this page](https://app.gitbook.com/@definer/s/definer/liquidate).

---
description: Bank Contract
---

# Bank

The`Bank` contract, as its name implied, mainly deals with the methods and variables related to the saving pool. It uses three variables to track the total amount of tokens in the pool and their allocations. For each `token`,

* `totalLoans[token]` tracks total amount of `token` lend.
* `totalReserve[token]` tracks total amount of `token` reserved.
* `totalCompound[token]` tracks total amount of `token` in compound

Therefore, the total amount of `token` in the pool is the summation of these three variables. The token utilization ratio `U`, token reservation ratio `R` and compound ratio `C` are defined accordingly. 

There are three categories of methods in the contract. 

_**The first category of methods deals with the total amount of tokens in the pool and their allocations. They are used to query the pool status of a token.**_

* getTotalDeposit
* getPoolAmount
* getTokenState
* getCapitalUtilizationRatio
* getCapitalCompoundRatio

_**The second category of methods deals with the deposit rate and borrow rate of tokens in the pool. They are used to update and get the rate indexes information of each specific token.**_

* newRateIndexCheckpoint
* getDepositRatePerBlock
* getBorrowRatePerBlock
* depositRateIndexNow
* borrowRateIndexNow
* getDepositAccruedRate
* getBorrowAccruedRate

_**The third category of methods deals with all the operations on the funds in the saving pool. These functions actually modify the recorded pool status.**_

* Deposit
* Withdraw
* Borrow
* Repay
* Update

For more details about the modifiers of the functions, please check the modifiers page.

## getTotalDepositStore

#### function head

`function getTotalDepositStore(address _token) public view returns(uint)`

* parameters:
  * \_token: The address of the token you want to query for.
* return:
  * The number of total deposit tokens.

#### description

The method returns the sum of `totalLoans[token]`,`totalReserve[token]` and `totalCompound[token]`.

## getPoolAmount

#### function head

`function getPoolAmount(address _token) public view returns(uint)`

* parameters
  * \_token: The address of the token you want to query for.
* return:
  * The total number of tokens that are not borrowed out in WeFarm's pool.

#### description

The method returns the sum of `totalCompound[token]` and `totalReserve[token]` .

## getTokenState

#### function head

`function getTokenState(address _token) public view returns (uint256 deposits, uint256 loans, uint256 reserveBalance, uint256 remainingAssets)`

* parameters
  * \_token: The address of the token you want to query for.
* return:
  * deposits: The total number of deposited tokens.
  * loans: The total number of borrowed tokens.
  * reserveBalance: The total number of tokens that are still in the reserve pool.
  * remainingAssets: The same as getPoolAmount.

#### description

The method returns total deposit, total loans, total reservation, and total available tokens of a specific token.

## getCapitalUtilizationRatio

#### function head:

`function getCapitalUtilizationRatio(address _token) public view returns(uint)`

* parameters:
  * \_token: The address of the token you want to query for.
* return:
  * The U ratio with the precision to 18 decimals.

#### description

The method returns `U` of a token, which is the ratio of the total loan to the total deposit.

## getCapitalCompoundRatio

#### function head

`function getCapitalCompoundRatio(address _token) public view returns(uint)`

* parameters: 
  * \_token; The address of the token you want to query for.
* return:
  * The C ratio with the precision to 18 decimals.

#### description

The method returns `C` of a token, which is the ratio of the total amount in Compound to the total deposit. 

## newRateIndexCheckpoint

#### function head

`function newRateIndexCheckpoint(address _token) public onlyAuthorized`

* parameters:
  * \_token: The address of the token you want to query for.

#### description

Add a new checkpoint both on the deposit rate index curve and the borrow rate index curve. Same as Compound, WeFarm uses rate index curve to track the accumulated rate of a token. The rate index curve is updated whenever any operation on that token happens. The interests of a token earned in a specific period could be derived from the rate index curve quickly. 

## getDepositRatePerBlock

#### function head

`function getDepositRatePerBlock(address _token) public view returns(uint)`

* parameters
  * \_token: The address of the token you want to query for.
* return:
  * The current deposit rate per block of the token.

#### description

Get the per block deposit rate of a token, and it is calculated as follows

$$
\text{Deposit Rate} = U\times \text{Borrow Rate} + C\times \text{Compound Supply Rate}
$$

## getBorrowRatePerBlock

#### function head

`function getBorrowRatePerBlock(address _token) public view returns(uint)`

* parameters:
  * \_token: The address of the token you want to query for.
* return:
  * The current borrowing rate per block of the token.

#### description

Get the per block borrow rate of a token. If the token is supported in Compoud, the borrow rate is determined by the borrow rate and supply rate of Compound as

$$
\text{Borrow Rate} = \text{Compound Supply Rate} \times 0.4 + \text{Compound Borrow Rate} \times 0.6
$$

Otherwise,

$$
\text{Borrow Rate} = 0.03 + U\times 0.15
$$

The numbers in the equations are the initial value of the variables and are configurable in `GlobalConfig` contract.

## depositRateIndexNow

#### function head

`function depositRateIndexNow(address _token) public view returns(uint)`

* parameters:
  * \_token: The address of the token you want to query for.
* return:
  * The current deposit rate index.

#### description

Get deposit rate index of the current block. If the current block is a checkpoint, this method returns the exact value on the deposit index curve. Otherwise, it returns an estimated value of the deposit rate index.

## borrowRateIndexNow

#### function head

`function borrowRateIndexNow(address _token) public view returns(uint)`

* parameters:
  * \_token: The address of the token you want to query for.
* return:
  * The current borrow rate index.

#### description

Get borrow rate index of the current block. If the current block is a checkpoint, this method returns the exact value on the borrow index curve. Otherwise, it returns an estimated value of the borrow rate index.

## getDepositAccruedRate

#### function head

`function getDepositAccruedRate(address _token, uint _depositRateRecordStart) external view returns (uint256)`

* parameters:
  * \_token: The address of the token you want to query for.
  * \_depositRateRecordStart: The start block you want to query accrued rate for.
*   return:
  * Return the accrued deposit rate given a token and a start block with precision to 18 decimals.

#### description

The deposit accrued rate in a block gap from $$B_1$$ to $$B_2$$is $$\text{Deposit Rate Index}(B_2)/\text{Deposit Rate Index}(B_1)$$ . Here$$B_1$$ is `_depositRateRecordStart` you specified in the parameter and $$B_2$$ should be the current block number. If you deposit `n` tokens at block $$B_1$$ , and the current block number is $$B_2$$, the tokens you have now should be $$\text{Deposit Rate Index}(B_2)/\text{Deposit Rate Index}(B_1) \times n$$.

There should be a rate index created on \_depositRateRecordStart block, otherwise, it will throw an error.

## getBorrowAccruedRate

#### function head

`function getBorrowAccruedRate(address _token, uint _borrowRateRecordStart) external view returns (uint256)`

* parameters: 
  * \_token: The address of the token that you want to query for.
  * \_borrowRateRecordStart: The start block you want to query accrued rate for.
* return:
  * Return the accrued borrow rate given a token and a start block with precision to 18 decimals.

#### description

The borrow accrued rate in a block gap from $$B_1$$ to $$B_2$$ is $$\text{Borrow Rate Index}(B_2)/\text{Borrow Rate Index}(B_1)$$ . It's similar to getDepositAccruedRate.

## Deposit

#### function head

`function deposit(address _to, address _token, uint256 _amount) external onlyAuthorized`

* parameters:
  * \_to: The address of the account that tries to deposit.
  * \_token: The address of the token you want to deposit.
  * \_amount: The volume of tokens that you want to deposit.

#### description

This method will create a new checkpoint on the rate index curve. It will deposit the token to the individual account of `msg.sender` in `Accounts` contract. Then, it will update the pool balance, i.e. the value of `totalReserve[token]` and `totalCompound[token]` , accordingly.

The deposited token will be first added to the pool reservation. If the reservation ratio `R` exceeds `20%`, the contract will deposit the token to Compound to reset `R` to be `15%`.

## Withdraw

#### function head

`function withdraw(address _from, address _token, uint256 _amount) external onlyAuthorized returns(uint)`

* parameters
  * \_from: The address of the account that tries to withdraw.
  * \_token: The address of the kind of token you want to withdraw.
  * \_amount: The volume of tokens you want to withdraw.

#### description

This method will create a new checkpoint on the rate index curve. The new deposit balance is tracked in the individual account of `msg.sender` in `Accounts` contract. The withdraw is allowed only if the account has enough balance and its loan value is still below the reduced borrow power after withdrawn. Then, the contract will update the pool balance by calling `update` method.

## Borrow

#### function head

`function borrow(address _from, address _token, uint256 _amount) external onlyAuthorized`

* parameters:
  * \_from: The account that tries to borrow tokens.
  * \_token: The address of the token that you want to borrow.
  * \_amount: The volume of tokens that the user tries to borrow.

#### description

This method will create a new checkpoint on the rate index curve. The new borrowed balance is tracked in the individual account of `msg.sender` in `Accounts` contract. The borrow  is allowed only if the added loan value is below the borrow power. Then, the contract will update the pool balance by calling `update` method.

## Repay

#### function head

`function repay(address _to, address _token, uint256 _amount) external onlyAuthorized returns(uint)`

* parameters:
  * \_to: The address that tries to repay.
  * \_token: The address of the token that the account wants to repay.
  * \_amount: The volume of tokens that the account wants to repay.

#### description

This method will create a new checkpoint on the rate index curve. The new borrowed balance is tracked in the individual account of `msg.sender` in `Accounts` contract. The borrow  is allowed only if the added loan value is below the borrow power. Then, the contract will update the pool balance by calling `update` method.

## Update

#### function head

`function update(address _token, uint _amount, ActionType _action) public onlyAuthorized returns(uint256 compoundAmount)`

* parameters: 
  * \_token: The address of the token you want to update.
  * \_amount: The volume of tokens involved in this operation.
  * \_action: The type of action that changes the pool amount.
    * DepositAction
    * RepayAction
    * WithdrawAction
    * BorrowAction

#### descrption

This method is called whenever the token balance in the saving pool is changed. It checks if there is enough amount of a token in the pool, i.e. the market liquidity. If an account borrows or withdraws. When the balance of a token that changes in the pool, this method updates the allocation of the token in the reservation and the Compound.

If an account deposits or repay such that the reservation ratio exceeds `20%`, the contract will deposit an extra amount of token to Compound and reset `R` to be 15%. On the other hand,  if an account withdraw or borrow such that the reservation ratio falls behind `10%`, the contract will withdraw the token from Compound and try to reset `R` to be `15%`. However, in this case, there might not be enough tokens in Compound so that the final `R` could be below `15%`.

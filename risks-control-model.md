---
description: >-
  Collateral is required to against default risks, it will be liquidated if it
  met the designed liquidation conditions
---

# Risks Control Model

## **Parameters**

1. \_targetAccountAddr: Address type. The account that a user is trying to liquidate.
2. \_targetToken: Address type. The address of the ERC20 token that you are going to spend to liquidate the target account.

## Design Overview

By calling this function, the caller tries to buy the collateral of a target account that has borrowed some target tokens from WeFarm, and it's liquidatable. The caller can buy the collateral with a 5% discount at a price from ChainLink, and the contract uses the received tokens to repay the target account's debt. The money the caller used to liquidate the target account should already be deposited to WeFarm instead of transferring into WeFarm in the liquidate function.

The target account may have multiple kinds of collateral tokens; in this case, the contract lets the caller buy these tokens at the order of each token's market liquidity. Let's assume that the market liquidity is ETH>USDT>DAI>USDC, and the target account has ETH, DAI, and USDC as collateral and borrows some USDT, and it's liquidatable. The caller has deposited some USDT and tries to liquidate the target account. The function will first compute the maximum USDT that the caller can send to the target, ensuring that the caller's account's borrow ETH won't exceed its borrow power. The caller will try to first buy ETH with USDT at a price that is 5% less than the price from chainlink. If selling all the ETH is enough to bring the target account's LTV less or equal to 60%, which makes sure that its borrow power is greater or equal to its borrow ETH, or the caller's maximum transferrable amount of USDT is not enough to buy all the ETH, we stop the liquidation process here. Otherwise, it will then try to buy DAI from the target and then try to buy USDC. 

Please check the pseudocode description section for a cleaner description.

To be liquidatable, an account's LTV should be between 85%\~95%. If the LTV is more significant than 85%, this account is at high risk of not repaying its debt. But if the LTV is larger than 95%, the collateral of the account is not enough for other accounts to liquidate it since the other account will buy the collateral of this account at a discount ratio of 5%.

### **Terminology**

**CBB**: Current borrow balance = principal + accrued interest

**MMRCV**: Maintaining Minimum Required Collateral Value = current borrow balance / Maintaining LTV ratio

**LDR:** Liquidation Discount ratio: The discount ratio the liquidator will get when buying other's assets during the liquidation process.

**CMPL**: Collateral Market Price at Liquidation: The market price of underlying collateral when a liquidation event happens. 

**CCV**: Current Collateral Value = CMPL \* Collateral Amount

**UAAL**: User asset at Liquidation: The maximum collateral can be liquidated or swapped.

**ILTV**: Initial LTV ratio of collateral token: 0.6 currently for most tokens

We have to make sure that:

$$
(CBB - (1-LDR)*UAAL)/(CCV-UAAL)\leq ILTV
$$

Which can derive to:

$$
UAAL= (CBB – CCV*ILTV)/(1-LDR-ILTV)
$$

## Examples

Let's assume that WeFarm supports only four kinds of tokens, and they're ETH, USDT, DAI, and USDC. Their liquidity order is ETH>USDT>DAI>USDC. And there are no interests involved in this process for simplification.

| Token Name     | ETH | USDT | DAI | USDC |
| -------------- | --- | ---- | --- | ---- |
| Liquidity Rank | 1   | 2    | 3   | 4    |
| Price          | 300 | 1    | 1   | 1    |

### 1. Target account has one kind of collateral, the caller tries to liquidate and can liquidate fully

Before liquidation:

User1:

1. Deposits: 100 USDT
2. Loans: 90 DAI
3. LTV: 0.9, liquidatable

User2:

1. Deposits 100 DAI
2. It calls liquidate(User1, DAIAdress)

Explanation

The maximum amount of DAI that User2 can transfer to User1 is 100 since it doesn't have any borrows.

$$
UAAL=(90-1*100*0.6)/(1-0.05-0.6)=85.7
$$

Since DAI's price is 1, and 85.7 < 100, User2 can pay the maximum value, which is 85.7 DAI. We call this fully liquidate.

After Liquidation

User1:

1. Deposits: 100 - 85.7 = 14.3 USDT
2. Loans: 90-85.7\*0.95 =  8.6 DAI
3. LTV: 0.6, not liquidatable

User2:

1. Deposits: 18.6 DAI, 85.7 USDT

### 2. Target account has one kind of collateral, the caller tries to liquidate and can only liquidate partially:

Before Liquidation:

User1:

1. Deposits: 100 USDT
2. Loans: 90 DAI
3. LTV: 0.9, liquidatable

User2:

1. Deposits 50 DAI
2. It calls liquidate(User1, DAIAdress)

Explanation:

Here, UAAL is computed the same way as the previous example.

$$
UAAL=(90-1*100*0.6)/(1-0.05-0.6)=85.7
$$

But here, User2 only has 50DAI, which is worth 50. 50 < 85.7, so User2 can't be swapped to the maximum value UAAL. That way, user2 only pays 50 DAI, and User1 will pay 50 / 0.95 = 52.6 USDC.

After liquidation

User1:

1. Deposits: 100 - 50/0.95 = 47.4 USDC 
2. Borrows: 90-50 = 40DAI
3. LTV: 40 \* 1 / 47.4 \* 1 = 0.84, not liquidatable

User2: 

1. Deposits: 50/0.95 = 52.6 USDC

Notice here although User2 doesn't fully liquidate User1, User1 is not liquidatable after liquidation. This is because there is a gap between the initial borrow LTV and the LTV to become liquidatable.

### **3. Target account has multiple kinds of collateral, and the caller tries to liquidate the user entirely.**

Before liquidation:

User1:

1. Deposits: 50 USDT, 50 USDC
2. Loans: 90 DAI
3. LTV: 0.9, liquidatable

User2: 

1. Deposits 100 DAI
2. It calls liquidate(User1, DAIAddress)

In the liquidation process, we iterate through all the tokens that WeFarm supports. To simplify it, I omitted this in the previous two examples. The process is the following: When we iterate one token, we compute a UAAL, and if the target account's deposit in the current token that we are visiting has a higher value than UAAL, which means that selling the current kind of token is enough to bring its borrow value less than its borrow power, we sell the UAAL value of the token to the liquidator and end liquidation. Otherwise, it means that selling all current tokens is not enough. We sell all the tokens to the liquidator in the current token and continue this process to the next token. At any time, if `target.borrowETH` < `target.borrowPower` or `liquidator.targetTokenBalance` = 0, we end this liquidate process.

Regarding the liquidity order, we first iterate ETH, but the target doesn't have any deposits in that token. We skip it. Then we meet USDT, and for USDT.

$$
UAAL=(90-1*100*0.6)/(1-0.05-0.6)=85.7
$$

The value of deposited USDT is only 50 for User1, smaller than 85.7, so we sell as much as possible. And now:

After selling USDT

User1: 

1. Deposits: 50 USDC
2. Loans: 90 - 50 \* 0.95 = 42.5 DAI

User2: 

1. Deposits: 50 USDT, 100 - 50 \* 0.95 = 52.5 DAI

We also skip `DAI` since the user doesn't have any deposit in DAI either. Finally, we use USDC.

$$
UAAL=(42.5-1*50*0.6)/(1-0.05-0.6)=35.7
$$

User1 has enough deposits in USDC, so it sells 35.7 to User2, end liquidation process.

After selling USDC, end the liquidation

User1: 

1. Deposits: 14.3 USDC
2. Loans: 42.5 - 35.7 \* 0.95 = 8.6 DAI

User2:

1. Deposits: 35.7 USDC, 50 USDC, 52.5 - 35.7 \* 0.95 = 18.6 DAI

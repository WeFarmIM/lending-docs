# Protocol Overview

WeFarm Savings protocol aggregates crypto deposits from lenders to the smart contract for users to borrow against the collateral asset they deposited. If there is unused capital in the contract, the protocol will deploy money automatically to market protocols like Compound, AAVE, etc. 

## Capital Reservation Ratio and Compound Ratio

For current available digital assets on the compound protocol (Ether, USD Coin, Augur, Dai, Sai, Wrapped BTC, Ox, Basic Attention Token), this enables WeFarm to supply/withdraw assets to Compound to improve the utilization rate of WeFarm. 

WeFarm auto supplies loan currency to “Compound Network” when capital reserve ratio (R) increases to a certain level, and auto withdraws loan currency from “Compound Network” when capital reservation ratio (R) drops to a range between 0 and 10. Here are the definitions of  Capital Utilization Rate (U ), Capital Compound Ratio (C), and Capital reserve ratio (R).

1. Capital Utilization Rate (U) = total loan outstanding / Total market deposit.
2. Capital Compound Ratio (C) = total capital in Compound / Total market deposit.
3. Capital reserve ratio (R) = 1 - U - C.

WeFarm always keeps the R between 10 and 20.  When R > 20,   it should signal Savings Pool Smart Contract to deposit to compound, which increases the value of C and reduces remaining reserve fund to 15% of total deposit. When R < 10, it should signal Savings Pool Smart Contract to withdraw from the money market, which decreases the value of C and increases the remaining reserve fund to 15% of the total deposit. (This reserve ratio range is globally configurable.)

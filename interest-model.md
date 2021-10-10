# Interest Model

### **Interest Rate Calculations**

#### **Definitions:**

* **u (utilization rate) **is the capital utilization rate of a specific token
* **c (Rate Curve Constant)**: 
* **s (Compound Supply Rate)**: the real-time supply rate on the money market
* **b (Compound Borrow Rate)**: the real-time borrow rate on the money market
* **sw (Compound Supply Rate Weight)**: the weight parameter of the Compound Supply Rate
* **bw (Compound Borrow Rate Weight)**: the weight parameter of the Compound Borrow Rate
* **r (Compound Supply Ratio)**: the percentage of capital deployed on the money market

#### Borrow Rate Model

$$Borrow APR= sw \times s + bw \times b + c \div (1-u)$$

When  $$u$$ >0.999,

$$c \div (1-u) = c \div (1-0.999)= c \times 1000$$

For assets that are not available on Compound or other money markets, Compound Supply Rate Weights=0, Compound Borrow Rate Weights=0,

In summary, two factors decided the Borrow APR, the prevailing market rate available in the market and the capital utilization rate in the WeFarm protocol. Also, it's a non-linear model. The borrowing interest can adapt quickly if the utilization of the pool approaches a relatively high level.

We have three different strategies base on different parameter sets: Conservative Mode, Moderate Model, and Aggressive Model. 

\|- Parameters -|- Conservative Model -|- Moderate Model -|- Aggressive Model -|

\| --- | --- | --- | --- |

\| Compound Supply Rate Weight | 0.1 | 0.3 | 0.9 |

\| Compound Borrow Rate Weight | 0.9 | 0.7 | 0.1 |

\| RateCurveConstant | 4 | 8 | 12 |

Below is how the borrow interest rate curve varies at different capital utilization levels based on three strategies.

#### Deposit Rate Model

$$Deposit Rate= r \times s + b \times u$$

For assets that are not available on Compound or other money markets, Compound Supply Rate Weights=0, Compound Borrow Rate Weights=0

### Interest Accounting System

**Definitions:**

* **Deposit principle**: the crypto assets that users deposited
* **Deposit interest**: interest that the depositor earned
* **Deposit storage interest:** the interest that depositor accrued
* **Deposit accrual interest:** the deposit interest that has not accrued
* **Deposit Interest per block:** interest that users earn for every block
* **BlocksPerYear:** annual expected blocks of the blockchain 

**Formular:**

****$$Deposit Interest Rate Per Block = BorrowAPR\div BlocksPerYear$$ 

$$Deposit Interest Per Block = (Deposit Principle+Deposit Storage Interest) \times Deposit Interest Rate Per Block$$ 

$$DepositInterest(block_t)=DepositInterest(block_t -_1))+DepositInterestPerBlock$$ 

**BorrowAPR** will be updated in the contract if any of the token depositors perform a transaction.

The user will accru interest earned between the last and the latest transaction block will be accrued if the user performed a transaction and will be added to the **Deposit Storage Interest. **

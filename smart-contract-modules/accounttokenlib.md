# AccountTokenLib

### Overview

This is the library that is used by the Accounts contract to track the balances of each user.

In the Accounts contract, there is a field `mapping(address => Account) public accounts;` that is used to record the balance status, and `Account` struct is:

```
struct Account {
    mapping(address => AccountTokenLib.TokenInfo) tokenInfos;
    uint128 depositBitmap;
    uint128 borrowBitmap;
}
```

The depositBitmap and borrowBitmap are used to check whether one user has depositings/borrowings for each specific token with less gas cost.

And the TokenInfo struct is the following:

```
struct TokenInfo {
    // Deposit info
    uint256 depositPrincipal;   // total deposit principal of ther user
    uint256 depositInterest;    // total deposit interest of the user
    uint256 lastDepositBlock;   // the block number of user's last deposit
    // Borrow info
    uint256 borrowPrincipal;    // total borrow principal of ther user
    uint256 borrowInterest;     // total borrow interest of ther user
    uint256 lastBorrowBlock;    // the block number of user's last borrow
}
```

For each operation one user interacting with our contract, we update `accounts` field.

## TokenInfoRegistry

Token Info Registry to manage Token information. The Owner of the contract allowed to update the information. TokenInfo struct stores Token Information, this includes: ERC20 Token address, Compound Token address, ChainLink Aggregator address etc.

```
    struct TokenInfo {
        // Token index, can store upto 255
        uint8 index;
        // ERC20 Token decimal
        uint8 decimals;
        // If token is enabled / disabled
        bool enabled;
        // Is ERC20 token charge transfer fee?
        bool isTransferFeeEnabled;
        // Is Token supported on Compound
        bool isSupportedOnCompound;
        // cToken address on Compound
        address cToken;
        // Chain Link Aggregator address for TOKEN/ETH pair
        address chainLinkAggregator;
        // Borrow LTV, by default 60%
        uint256 borrowLTV;
        // Liquidation threshold, by default 85%
        uint256 liquidationThreshold;
        // Liquidation discount ratio, by default 95%
        uint256 liquidationDiscountRatio;
    }
```


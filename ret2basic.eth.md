**Issue category**
High

**Issue title**
Division before multiplication in `calculateFee()`

**Where**
https://github.com/AuditoneCodebase/CTF_challenge_30.6.2023/blob/3726d1c2fb5f98335c6b7386668a6b16bf2729df/Exchange.sol#L22

**Impact**
Fee will always be calculated as 0.

**Description**
When calculating the fee, `(_coins / totalCoins) * exchangeFee` will always return 0 since `totalCoins` is always larger than `_coins`. This allows fee-free exchanges forever.

**Recommendations to fix**
Change the expression to `_coins * exchangeFee * multiplier / totalCoins / multiplier`, where a `multiplier` should be used to prevent rounding issue.

**Additional context**
None.

**Comments by AuditOne**


------

**Issue category**
High

**Issue title**
`executeExchange()` does not give user anything

**Where**
https://github.com/AuditoneCodebase/CTF_challenge_30.6.2023/blob/3726d1c2fb5f98335c6b7386668a6b16bf2729df/Exchange.sol#L25-L32

**Impact**
Users would send ETH to the contract and get nothing back.

**Description**
This function currently does not implement any logic for the "exchange". It only collects ETH from user, charges user a fee and sends back the change. Users would get nothing from calling this function.

**Recommendations to fix**
Implement exchange logic.

**Additional context**
None.

**Comments by AuditOne**


------

**Issue category**
High

**Issue title**
`executeExchange()` sends the change to the owner instead of user

**Where**
https://github.com/AuditoneCodebase/CTF_challenge_30.6.2023/blob/3726d1c2fb5f98335c6b7386668a6b16bf2729df/Exchange.sol#L30

**Impact**
Users won't get the change and owner is over-paid.

**Description**
Based on the context, "change" should go back to the person who pays for an item. In this scenario, `msg.sender` is paying so I believe the change should go back to `msg.sender`.

**Recommendations to fix**
Change this line to `payable(msg.sender).transfer(change);`.

**Additional context**
None.

**Comments by AuditOne**


------

**Issue category**
Medium

**Issue title**
Missing ownership transfer functionalities

**Where**
https://github.com/AuditoneCodebase/CTF_challenge_30.6.2023/blob/3726d1c2fb5f98335c6b7386668a6b16bf2729df/Exchange.sol#L11

**Impact**
Owner can't be changed.

**Description**
The `owner` role is set in the constructor and there is no way to update it once the contract is deployed. It is a good idea to implement ownership transfer functionalities just in case the owner role needs to be updated in the future.

**Recommendations to fix**
Use OpenZeppelin 2-step ownership transfer library.

**Additional context**
None.

**Comments by AuditOne**


------

**Issue category**
Medium

**Issue title**
Fee needs to be redesigned

**Where**
https://github.com/AuditoneCodebase/CTF_challenge_30.6.2023/blob/3726d1c2fb5f98335c6b7386668a6b16bf2729df/Exchange.sol#L12

**Impact**
Fee is too small in current implementation

**Description**
Currently `exchangeFee` is set to be 40 in the constructor. That means the maximum fee in the current state is 40, because `(_coins / totalCoins) * exchangeFee` can't be anything higher than 40. This fee (40 wei) seems too small for the business logic.

**Recommendations to fix**
Redesign fee computation.

**Additional context**
None.

**Comments by AuditOne**


------

**Issue category**
Medium

**Issue title**
Don't use `transfer()` for transfering ETH

**Where**
https://github.com/AuditoneCodebase/CTF_challenge_30.6.2023/blob/3726d1c2fb5f98335c6b7386668a6b16bf2729df/Exchange.sol#L30

**Impact**
`transfer()` can be stuck because of 2300 gas limit and the `executeExchange()` will go into DoS state.

**Description**
In Solidity, `transfer()` and `send()` are hardcoded to forward only 2300 gas. This is not enough for the Ethereum mainnet, not to speak of other EVM-compatible chains with higher gas cost (such as zkSync Era).

**Recommendations to fix**
Use low-level call to send ETH:

```solidity
(bool success, ) = payable(msg.sender).call{value: change}("");
require(success, "change transfer failed");
```

**Additional context**
None.

**Comments by AuditOne**


------

**Issue category**
QA

**Issue title**
Floating pragma

**Where**
https://github.com/AuditoneCodebase/CTF_challenge_30.6.2023/blob/3726d1c2fb5f98335c6b7386668a6b16bf2729df/Exchange.sol#L2

**Impact**
Could lead to unexpected behavior.

**Description**
It is a best practice to use fixed pragma instead of floating pragma. Sometimes the floating pragma may lead to compiling with too old or too new compiler version, which has unexpected behavior.

**Recommendations to fix**
Use fixed pragma such as `pragma solidity 0.8.17;`.

**Additional context**
None.

**Comments by AuditOne**


------

**Issue category**
QA

**Issue title**
Unset visibility for `totalCoins`

**Where**
https://github.com/AuditoneCodebase/CTF_challenge_30.6.2023/blob/3726d1c2fb5f98335c6b7386668a6b16bf2729df/Exchange.sol#L8

**Impact**
`totalCoins` is set to `internal` by default if no visibility is set.

**Description**
For state variables, unset visibility defaults to `internal`. This may not be align with developer's intention.

**Recommendations to fix**
Set visibility for `totalCoins`.

**Additional context**
None.

**Comments by AuditOne**


------

**Issue category**
QA

**Issue title**
Missing checks for `_totalCoins`

**Where**
https://github.com/AuditoneCodebase/CTF_challenge_30.6.2023/blob/3726d1c2fb5f98335c6b7386668a6b16bf2729df/Exchange.sol#L13

**Impact**
`_totalCoins` can be wrong since there is no checks on it.

**Description**
There is no check in the constructor for `_totalCoins`. If the input parameter supplied is wrong, for example, `_totalCoins == 0`, then `calculateFee()` will be stuck in DoS state because of divide-by-zero error. Many other things can happen if `_totalCoins` is wrong.

**Recommendations to fix**
Implement checks for `_totalCoins`, such as upper bound and lower bound.

**Additional context**
None.

**Comments by AuditOne**


------

**Issue category**
QA

**Issue title**
Missing checks for the input `_fee`

**Where**
https://github.com/AuditoneCodebase/CTF_challenge_30.6.2023/blob/3726d1c2fb5f98335c6b7386668a6b16bf2729df/Exchange.sol#L18

**Impact**
`_fee` can be wrong since there is no checks on it.

**Description**
`setExchangeFee()` does not implement checks on `_fee`, therefore `_fee` can be too high or too low depending on the context.

**Recommendations to fix**
Implement checks for `_fee`, such as upper bound and lower bound.

**Additional context**
None.

**Comments by AuditOne**


------

**Issue category**
QA

**Issue title**
Use pull-over-push pattern when sending out change

**Where**
https://github.com/AuditoneCodebase/CTF_challenge_30.6.2023/blob/3726d1c2fb5f98335c6b7386668a6b16bf2729df/Exchange.sol#L29-L31

**Impact**
ETH transfer can be stuck if the target does not accept ETH.

**Description**
If a contract (instead of EOA) calls `executeExchange()`, there is no guarantee that the contract can receive ETH. If that is case, the `transfer()` call will fail. It is better to implement pull-over-push pattern, for example, create a mapping `balances` that records users' change, and then implement a function `withdraw()` to let users take out their change.

**Recommendations to fix**
Implement pull-over-push pattern.

**Additional context**
None.

**Comments by AuditOne**


**Issue 1**

**Issue category**

Medium

**Issue title**

`executeExchange` never transfers the `_coins` to `msg.sender`

**Where**
https://github.com/AuditoneCodebase/CTF_challenge_30.6.2023/blob/3726d1c2fb5f98335c6b7386668a6b16bf2729df/Exchange.sol#L25

**Impact**
`msg.sender` loses the transfered ether

**Description**
When a user calls `executeExchange`, he/she transfer the ether to the contract, the contract later on transfers the fee to the owner, but no tokens are transfered.

**Recommendations to fix**
Add the logic to transfer the tokens to the msg.sender

**Additional context**
Add any other context about the problem here.

**Comments by AuditOne**
AuditOne team can comment here

---
**Issue 2**

**Issue category**
Low

**Issue title**
Loss of Precision in the `calculateFee` function 

**Where**
https://github.com/AuditoneCodebase/CTF_challenge_30.6.2023/blob/3726d1c2fb5f98335c6b7386668a6b16bf2729df/Exchange.sol#L22

**Impact**
The owner may lose fee over multiple trades

**Description**
With the assumption that, `_coins <= totalCoins`, the function `calculateFee` performs division first and then multiplication.
This can result in rounding down of the value as solidity behaves in this manner which results in less fee collected for a trade.

**Recommendations to fix**
Update the code on Line#22 to perform multiplication first and then division

```
        return (_coins * exchangeFee) / totalCoins;
```

**Additional context**
Add any other context about the problem here.

**Comments by AuditOne**
AuditOne team can comment here

---
**Issue 3**

**Issue category**
Low

**Issue title**
use `call()` instead of `transfer()`

**Where**
https://github.com/AuditoneCodebase/CTF_challenge_30.6.2023/blob/3726d1c2fb5f98335c6b7386668a6b16bf2729df/Exchange.sol#L30

**Impact**
The executeExchange function will revert

**Description**
The use of the deprecated transfer() function for an address will inevitably make the transaction fail when:

1. The claimer smart contract does not implement a payable function.
2. The claimer smart contract does implement a payable fallback which uses more than 2300 gas unit.
3. The claimer smart contract implements a payable fallback function that needs less than 2300 gas units but is called through proxy, raising the call's gas usage above 2300.

Additionally, using higher than 2300 gas might be mandatory for some multisig wallets.

**Recommendations to fix**
using `call()` instead of `transfer()`

**Additional context**
Add any other context about the problem here.

**Comments by AuditOne**
AuditOne team can comment here

---

**Issue 4**

**Issue category**
Informational/QA

**Issue title**
Use the latest solidity version instead of floating pragma

**Where**
https://github.com/AuditoneCodebase/CTF_challenge_30.6.2023/blob/main/Exchange.sol

**Issues**
1. Floating Pragma issue
2. Events are not emitted
3. Owner can set a very high exchange fee

**Recommendations to fix**
1. Update to latest solidity version
2. Emit events on important state changes and index them
3. Define a state varaible like MAX_FEE = 20; and add a check in the setExchangeFee this ensures that the owner cannot set  exchange fee more than MAX_FEE.

**Additional context**
Add any other context about the problem here.

**Comments by AuditOne**
AuditOne team can comment here

---


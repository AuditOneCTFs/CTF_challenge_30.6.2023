**Issue category**
Low

**Issue title**
Use the latest solidity version instead of floating pragma

**Where**
CTF_challenge_30.6.2023/blob/3726d1c2fb5f98335c6b7386668a6b16bf2729df/Exchange.sol#L2

**Impact**
an outdated compiler version that might introduce bugs that affect the contract system negatively.

Reference - https://swcregistry.io/docs/SWC-103

**Description**
The project is using solidity version ^0.8.0

Contracts should be deployed with the same compiler version and flags that they have been tested with thoroughly. Locking the pragma helps to ensure that contracts do not accidentally get deployed using

**Recommendations to fix**
Lock the pragma version and also consider known bugs (https://github.com/ethereum/solidity/releases) for the compiler version that is chosen.

**Additional context**
Add any other context about the problem here.

**Comments by AuditOne**
AuditOne team can comment here

---

**Issue title**
Write the title for the issue

**Where**
Where the issue is found

**Impact**
What is the negative impact if the issue is present

**Description**
Describe the issue in detail

**Recommendations to fix**
Provide recommendations to fix this issue

**Additional context**
Add any other context about the problem here.

**Comments by AuditOne**
AuditOne team can comment here

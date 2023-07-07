
**Issue category High**

**Issue 1**
Loss of precision

Where https://github.com/AuditoneCodebase/CTF_challenge_30.6.2023/blob/3726d1c2fb5f98335c6b7386668a6b16bf2729df/Exchange.sol#L22

Impact
Fee will always be calculated as 0.

Description 
In the calculateFee function, the coins are divided by totalcoins before multiplying with exchange fee. If the coins are in small numbers,
since solidity does not support fractions by default, the result will be 0 resulting in 0 exchange fee. 
It is strongly advised to always multiply before dividing as this prevents in rouding issues.

Recommendations
Change the logic to _coins * exchangeFee * bps / totalCoins / bps
BPS also referred as basis points to maintain proper scaling in math computations in Solidity.

Additional context None.


Comments by AuditOne

**Issue category High**

**Issue 2**
No scaling is implemented

Impact
As there is no scaling implemented, the relationship between coins and fee is not effectively translating.
Example, the total coins could be in millions and exchangefee represented here either an absolute 40 or 40%.

Using scaling, if the intention was 40%, then the fee should be represented in the same scale by multiplying 40 in BPS.

Description
Missing scaling

Recommendations
Implemented scaling

Additional context None.


Comments by AuditOne


**Issue category High**

**Issue 3**
executeExchange() receiver of exchanged value is incorrecrt.

Where 
https://github.com/AuditoneCodebase/CTF_challenge_30.6.2023/blob/3726d1c2fb5f98335c6b7386668a6b16bf2729df/Exchange.sol#L30

Impact 
msg.sender is not receiving the coins in exchange for the value he pays in executeExchange function/.

Description 
The executeExchange should return the converted coins to the msg.sender. But, instead, the coins are transferred to owner.
This is outright stealing of coins from the user who wanted to convert tokens.

Recommendations 
payable(msg.sender).transfer(change);

Additional context None.

Comments by AuditOne


**Issue category Medium**

**Issue 4**

There is no check on the total coins value passed in the constructor. If the number is initialized to 0, then the exchange fee will be permanently 0.
Also, if the total coins is 0, what is the user exchanging ? There is a complete gap in the validations to prevent transfers beyound total supply.

Impact
Incorrect accounting, as there is no relationshipo between totalcoins and their ownership, the transfer and fee computations are effectively tied to supply.
This gap is hidden behind the transfer it is going back to the owner of the contract.

Recommendations 
build a relationship between the total coins and transfers. The contract if owns all total coins, then on exchange, the coins should move from contract to msg.sender
on completion of executeExchange.

Additional context None.

Comments by AuditOne


**Issue category Medium**

**Issue 5**

Model the fee for the contract. Configuring 40 as fee is not defining the scale for conversion.
If 40 was represenation of 40%,the scaling factor and also in setExchangeFee function, a validation logic is needed to ensure only value range of values are accepted.
Example, if fee is represented in percentage and a design for fee range is 0 to 60% as fees, values above 60 should not be accepted.

Where
https://github.com/betharavikiran/CTF_challenge_30.6.2023/blob/3726d1c2fb5f98335c6b7386668a6b16bf2729df/Exchange.sol#L12

Impact 
Validation in setExchangeFee to accept a prescribed range of values only.

Recommendations 
add validation logic based on fee design
require(fee >=0,"invalid value");
require(fee < 60,"60% max fee");


Additional context None.

Comments by AuditOne

**Issues category QA**

**Issue 6**

Issues:

Category QA

a) Owner can set exchange rate and it is centralized.

b) loss of private keys of the owner account bears the risk for loss of all funds.

c) floating pragma is used. Floating pragma should be used only by library contracts and project should always lock the pragma before testing
   testing should be done with locked pragma to avoid any expected behaviour on the deploy on mainnet.

d) Data type should be uint256 for exchange fee and total coin. It is better to explicitly declare the data types.   

e) Natspec is missing for all the functions of this contract   



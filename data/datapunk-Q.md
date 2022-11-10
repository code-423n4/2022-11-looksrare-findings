
QA1: setRate does not emit event
https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/modules/interest-rate/InterestRateCredit.sol#L74

QA2: 
Use staticCall instead of call for decimals(): https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/utils/CreditLib.sol#L142

QA3: CONSIDER TWO-PHASE OWNERSHIP TRANSFER
Admin calls `updateOwner` and `updateOperator` functions to transfers ownership and operatorship to the new addresses directly. As such, there is a risk that the ownership/operatorship is transferred to an invalid address, thus causing the contract to be without an owner/operator.

https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/utils/SpigotLib.sol#L178
https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/utils/SpigotLib.sol#L187

Recommendation
Consider implementing a two step process where the existing owner/operator nominates an account and the nominated account needs to call an acceptOwnership()/acceptOperator() function for the transfer to fully succeed. This ensures the nominated EOA account is a valid and active account.

QA4:
[`close()`](https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/modules/credit/LineOfCredit.sol#L388) is subject to reentrance by lender. 
`close` has external call to ETH and ERC20 transfers, thus subject to reentrancy.

add reentrancy guard to the function.

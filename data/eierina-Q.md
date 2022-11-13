# [NC-01] OwnableTwoSteps Status default relay on states order

## Impact
No explicit initialization of [Status public ownershipStatus](https://github.com/code-423n4/2022-11-looksrare/blob/e3b2c053f722b0ca2dce3a3eb06f64859b8b7a6f/contracts/OwnableTwoSteps.sol#L25 enum relies on enum ordering, something that is controlled outside and there is no test for if Status.NoOngoingTransfer being the default state (0 / uninitialized)) means that [OwnableTwoSteps](https://github.com/code-423n4/2022-11-looksrare/blob/e42ac05b3b740292422a725e9b57687e62d32c67/contracts/OwnableTwoSteps.sol) contract relies on [Status](https://github.com/code-423n4/2022-11-looksrare/blob/e42ac05b3b740292422a725e9b57687e62d32c67/contracts/interfaces/IOwnableTwoSteps.sol#L8-L12) enum states order, or on [NoOngoingTransfer](https://github.com/code-423n4/2022-11-looksrare/blob/e42ac05b3b740292422a725e9b57687e62d32c67/contracts/interfaces/IOwnableTwoSteps.sol#L9) being the very first enum state. Beside no initialization, the ownershipStatus is reset by use of delete, which again relies on the default state of the underlying type.

There is no test for the [NoOngoingTransfer](https://github.com/code-423n4/2022-11-looksrare/blob/e42ac05b3b740292422a725e9b57687e62d32c67/contracts/interfaces/IOwnableTwoSteps.sol#L9) being equal to the underlying type default, therefore there is no guarantee that the states cannot be reordered during maintenance, breaking the [OwnableTwoSteps](https://github.com/code-423n4/2022-11-looksrare/blob/e42ac05b3b740292422a725e9b57687e62d32c67/contracts/OwnableTwoSteps.sol) contract.

## Proof of Concept
https://github.com/code-423n4/2022-11-looksrare/blob/e42ac05b3b740292422a725e9b57687e62d32c67/contracts/interfaces/IOwnableTwoSteps.sol#L9
https://github.com/code-423n4/2022-11-looksrare/blob/e3b2c053f722b0ca2dce3a3eb06f64859b8b7a6f/contracts/OwnableTwoSteps.sol#L25
https://github.com/code-423n4/2022-11-looksrare/blob/e3b2c053f722b0ca2dce3a3eb06f64859b8b7a6f/contracts/OwnableTwoSteps.sol#L60
https://github.com/code-423n4/2022-11-looksrare/blob/e3b2c053f722b0ca2dce3a3eb06f64859b8b7a6f/contracts/OwnableTwoSteps.sol#L74
https://github.com/code-423n4/2022-11-looksrare/blob/e3b2c053f722b0ca2dce3a3eb06f64859b8b7a6f/contracts/OwnableTwoSteps.sol#L88

## Tools Used
n/a

## Recommended Mitigation Steps
Use explicit initialiaztion of NoOngoingTransfer in the following places:
https://github.com/code-423n4/2022-11-looksrare/blob/e3b2c053f722b0ca2dce3a3eb06f64859b8b7a6f/contracts/OwnableTwoSteps.sol#L25
https://github.com/code-423n4/2022-11-looksrare/blob/e3b2c053f722b0ca2dce3a3eb06f64859b8b7a6f/contracts/OwnableTwoSteps.sol#L60
https://github.com/code-423n4/2022-11-looksrare/blob/e3b2c053f722b0ca2dce3a3eb06f64859b8b7a6f/contracts/OwnableTwoSteps.sol#L74
https://github.com/code-423n4/2022-11-looksrare/blob/e3b2c053f722b0ca2dce3a3eb06f64859b8b7a6f/contracts/OwnableTwoSteps.sol#L88


# [NC-02] Transfers can burn

## Impact
ERC20 OpenZeppelin contracts recently removed the check for zero address. While I heard various discussion about the change, the only one that made sense to me was that the ERC20 contract should be generic and not a catch-all error conditions. Indeed, someone may actually need to send to the zero address. So the purpose of the contract should drive the need for the check or the absence of the zero address check.

Since the zero is the default for a non initialized address, and since the purpose of the LowLevelERCXXXTransfer / LowLevelETH contracts ([#1](https://github.com/code-423n4/2022-11-looksrare/blob/e3b2c053f722b0ca2dce3a3eb06f64859b8b7a6f/contracts/lowLevelCallers/LowLevelERC1155Transfer.sol#L31), [#2](https://github.com/code-423n4/2022-11-looksrare/blob/e3b2c053f722b0ca2dce3a3eb06f64859b8b7a6f/contracts/lowLevelCallers/LowLevelERC1155Transfer.sol#L53), [#3](https://github.com/code-423n4/2022-11-looksrare/blob/e3b2c053f722b0ca2dce3a3eb06f64859b8b7a6f/contracts/lowLevelCallers/LowLevelERC20Transfer.sol#L31), [#4](https://github.com/code-423n4/2022-11-looksrare/blob/e3b2c053f722b0ca2dce3a3eb06f64859b8b7a6f/contracts/lowLevelCallers/LowLevelERC20Transfer.sol#L51), [#5](https://github.com/code-423n4/2022-11-looksrare/blob/e3b2c053f722b0ca2dce3a3eb06f64859b8b7a6f/contracts/lowLevelCallers/LowLevelERC721Transfer.sol#L27), [#6](https://github.com/code-423n4/2022-11-looksrare/blob/e3b2c053f722b0ca2dce3a3eb06f64859b8b7a6f/contracts/lowLevelCallers/LowLevelETH.sol#L23), [#7](https://github.com/code-423n4/2022-11-looksrare/blob/e3b2c053f722b0ca2dce3a3eb06f64859b8b7a6f/contracts/lowLevelCallers/LowLevelETH.sol#L46), as also of the TokenTransferrer contracts ([#8](https://github.com/code-423n4/2022-11-looksrare/blob/e3b2c053f722b0ca2dce3a3eb06f64859b8b7a6f/contracts/TokenTransferrer.sol#L22), [#9](https://github.com/code-423n4/2022-11-looksrare/blob/e3b2c053f722b0ca2dce3a3eb06f64859b8b7a6f/contracts/TokenTransferrer.sol#L24)) are specific and not in any case the burn of the tokens, then a check for zero address on the recipient is a valid precaution in the case a path to a zero address recipient exists (whether it is human error, or by an attacker's effort).

One of such cases, for example, may be the lack of initialization of the _proxyFeeData\[proxy\].recipient by human error in [LooksRareAggregator](https://github.com/code-423n4/2022-11-looksrare/blob/e3b2c053f722b0ca2dce3a3eb06f64859b8b7a6f/contracts/LooksRareAggregator.sol) contract's [setFee](https://github.com/code-423n4/2022-11-looksrare/blob/e3b2c053f722b0ca2dce3a3eb06f64859b8b7a6f/contracts/LooksRareAggregator.sol#L153) which would cause fee transfers to be burn to zero address.

## Proof of Concept

https://github.com/code-423n4/2022-11-looksrare/blob/e3b2c053f722b0ca2dce3a3eb06f64859b8b7a6f/contracts/lowLevelCallers/LowLevelERC1155Transfer.sol#L31
https://github.com/code-423n4/2022-11-looksrare/blob/e3b2c053f722b0ca2dce3a3eb06f64859b8b7a6f/contracts/lowLevelCallers/LowLevelERC1155Transfer.sol#L53
https://github.com/code-423n4/2022-11-looksrare/blob/e3b2c053f722b0ca2dce3a3eb06f64859b8b7a6f/contracts/lowLevelCallers/LowLevelERC20Transfer.sol#L31
https://github.com/code-423n4/2022-11-looksrare/blob/e3b2c053f722b0ca2dce3a3eb06f64859b8b7a6f/contracts/lowLevelCallers/LowLevelERC20Transfer.sol#L51
https://github.com/code-423n4/2022-11-looksrare/blob/e3b2c053f722b0ca2dce3a3eb06f64859b8b7a6f/contracts/lowLevelCallers/LowLevelERC721Transfer.sol#L27
https://github.com/code-423n4/2022-11-looksrare/blob/e3b2c053f722b0ca2dce3a3eb06f64859b8b7a6f/contracts/lowLevelCallers/LowLevelETH.sol#L23
https://github.com/code-423n4/2022-11-looksrare/blob/e3b2c053f722b0ca2dce3a3eb06f64859b8b7a6f/contracts/lowLevelCallers/LowLevelETH.sol#L46
https://github.com/code-423n4/2022-11-looksrare/blob/e3b2c053f722b0ca2dce3a3eb06f64859b8b7a6f/contracts/TokenTransferrer.sol#L22
https://github.com/code-423n4/2022-11-looksrare/blob/e3b2c053f722b0ca2dce3a3eb06f64859b8b7a6f/contracts/TokenTransferrer.sol#L24

https://github.com/code-423n4/2022-11-looksrare/blob/e3b2c053f722b0ca2dce3a3eb06f64859b8b7a6f/contracts/LooksRareAggregator.sol#L153

## Tools Used
n/a

## Recommended Mitigation Steps
Add zero address check to transfer recipients.
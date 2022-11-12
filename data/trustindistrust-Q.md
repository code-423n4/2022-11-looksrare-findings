## Non-Critical

### Use explicit semantic for setting Status enum instead of implicit operation

Locations:
[1](https://github.com/code-423n4/2022-11-looksrare/blob/e3b2c053f722b0ca2dce3a3eb06f64859b8b7a6f/contracts/OwnableTwoSteps.sol#L60)
[2](https://github.com/code-423n4/2022-11-looksrare/blob/e3b2c053f722b0ca2dce3a3eb06f64859b8b7a6f/contracts/OwnableTwoSteps.sol#L74)
[3](https://github.com/code-423n4/2022-11-looksrare/blob/e3b2c053f722b0ca2dce3a3eb06f64859b8b7a6f/contracts/OwnableTwoSteps.sol#L88)

#### Description

While `delete ownershipStatus` is semantically identical to `ownershipStatus = Status.NoOngoingTransfer`, the latter is more explicit and clear about what is happening. The code as-is relies on the reader understanding that the deletion sets the value to 0, which is the same as the intended enum value.

#### Suggested course of action

Modify the locations noted to use an explicit form of the statement for improved clarity.
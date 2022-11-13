# ReentrancyGuard can save gas during deployment if status manages only values 0 and 1
This implies changing the contract to

```
abstract contract ReentrancyGuard is IReentrancyGuard {
    uint256 private _status;

    constructor() {}

    /**
     * @notice Modifier to wrap functions to prevent reentrancy calls
     */
    modifier nonReentrant() {
        if (_status == 1) revert ReentrancyFail();
        _status = 1;
        _;
        _status = 0;
    }
}
```

# OwnableTwoSteps#cancelOwnershipTransfer can save gas by caching ownershipStatus state variable
This state variable is accessed 4 times. This can be reduced to two by caching its value.
```
function cancelOwnershipTransfer() external onlyOwner {
    _ownershipStatus = ownershipStatus
    if (_ownershipStatus == Status.NoOngoingTransfer) revert NoOngoingTransferInProgress();

    if (_ownershipStatus == Status.TransferInProgress) {
        delete potentialOwner;
    } else if (_ownershipStatus == Status.RenouncementInProgress) {
        delete earliestOwnershipRenouncementTime;
    }

    delete ownershipStatus;

    emit CancelOwnershipTransfer();
}
```

# OwnableTwoSteps#confirmOwnershipTransfer can omit one state variable access
Last ```owner``` access can be avoided by replacing [this line](https://github.com/code-423n4/2022-11-looksrare/blob/e3b2c053f722b0ca2dce3a3eb06f64859b8b7a6f/contracts/OwnableTwoSteps.sol#L91) for ```emit NewOwner(msg.sender);```

# OwnableTwoSteps#initiateOwnershipTransfer can omit one state variable access
Giving modifier onlyOwner, we can assure that```msg.sender = owner```. This means that we can avoid last ```owner``` access by replacing [this line](https://github.com/code-423n4/2022-11-looksrare/blob/e3b2c053f722b0ca2dce3a3eb06f64859b8b7a6f/contracts/OwnableTwoSteps.sol#L104) for ```emit InitiateOwnershipTransfer(msg.sender, newPotentialOwner);```


# OwnableTwoSteps#initiateOwnershipRenouncement can omit one state variable access
We can avoid one access to state variable ```earliestOwnershipRenouncementTime``` by replacing [these lines](https://github.com/code-423n4/2022-11-looksrare/blob/e3b2c053f722b0ca2dce3a3eb06f64859b8b7a6f/contracts/OwnableTwoSteps.sol#L114-L116) for:

```solidity
uint256 _earliestOwnershipRenouncementTime = block.timestamp + delay;
earliestOwnershipRenouncementTime = _earliestOwnershipRenouncementTime

emit InitiateOwnershipRenouncement(_earliestOwnershipRenouncementTime);
```

# LooksRareAgregator#setFee can avoid double state variable access
[These lines](https://github.com/code-423n4/2022-11-looksrare/blob/e3b2c053f722b0ca2dce3a3eb06f64859b8b7a6f/contracts/LooksRareAggregator.sol#L159-L160) can be replaced to avoid double ```_proxyFeeData[proxy]``` access for:
```solidity
    FeeData newFeeData = {bp: bp, recipient: recipient };
    _proxyFeeData[proxy] = newFeeData;
```
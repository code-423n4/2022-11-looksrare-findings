For security, SeaportProxy.sol should only received eth from seaport marketplace.
```
    receive() external payable {
        // Reject unexpected address sending ether, only accecpt Seaport refunding.
        require(msg.sender == address(marketplace), "Unexpected ether sender");
    }
```
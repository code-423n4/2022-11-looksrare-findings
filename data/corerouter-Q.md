1. Need to check whether value has changed before emitting value change events.
https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/LooksRareAggregator.sol#L132-L135

function addFunction(address proxy, bytes4 selector) external onlyOwner {
        _proxyFunctionSelectors[proxy][selector] = true;
        emit FunctionAdded(proxy, selector);
}

Here the codes emits the event without checking whether the function selector has been added before. It is suggested to change the codes to avoid emitting the messages when the function selector has already been added, as shown below.

function addFunction(address proxy, bytes4 selector) external onlyOwner {
      if (!_proxyFunctionSelectors[proxy][selector] ) {
          _proxyFunctionSelectors[proxy][selector] = true;
          emit FunctionAdded(proxy, selector);
     }
}


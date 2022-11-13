# LOW


## 1. Unused/empty receive()/fallback() function

If the intention is for the Ether to be used, the function should call another function, otherwise it should revert (e.g. `require(msg.sender == address(weth))`). Having no access control on the function means that someone may send Ether to the contract, and have no way to get anything back out, which is a loss of funds

```
86	receive() external payable {}
```
https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/proxies/SeaportProxy.sol
```
220	receive() external payable {}
```
https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/LooksRareAggregator.sol


## 2. Immutable addresses lack zero-address check

Constructors should check the address written in an immutable address variable is not the zero address.

```
39	aggregator = _aggregator;
```
https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/proxies/LooksRareProxy.sol
```
47	aggregator = _aggregator;
```
https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/proxies/SeaportProxy.sol


# NON-CRITICAL


## 1. Non-library/interface files should use fixed compiler versions, not floating ones

In the contracts, floating pragmas should not be used. Contracts should be deployed with the same compiler version and flags that they have been tested with thoroughly. Locking the pragma helps to ensure that contracts do not accidentally get deployed using, for example, an outdated compiler version that might introduce bugs that affect the contract system negatively.

Proof of Concept
https://swcregistry.io/docs/SWC-103


https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/ReentrancyGuard.sol
https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/OwnableTwoSteps.sol
https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/SignatureChecker.sol
https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/lowLevelCallers/LowLevelERC20Approve.sol
https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/lowLevelCallers/LowLevelERC20Transfer.sol
https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/lowLevelCallers/LowLevelERC721Transfer.sol
https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/lowLevelCallers/LowLevelERC1155Transfer.sol
https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/lowLevelCallers/LowLevelETH.sol
https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/interfaces/IERC20.sol
https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/interfaces/IERC721.sol
https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/interfaces/IERC1155.sol


## 2. Consider adding checks for signature malleability

Use OpenZeppelin’s `ECDSA` contract rather than calling `ecrecover()` directly

```
60	signer = ecrecover(hash, v, r, s);
```
https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/SignatureChecker.sol


## 3. Event is missing `indexed` fields

Index event fields make the field more quickly accessible to off-chain tools that parse events. Each `event` should use three `indexed` fields if there are three or more fields, and gas usage is not particularly of concern for the events in question. If there are fewer than three fields, all of the fields should be indexed.

```
44	event FeeUpdated(address proxy, uint256 bp, address recipient);
51	event FunctionAdded(address indexed proxy, bytes4 selector);
56	event FunctionRemoved(address indexed proxy, bytes4 selector)
```
https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/interfaces/ILooksRareAggregator.sol
```
5	event Transfer(address indexed from, address indexed to, uint256 value);
7	event Approval(address indexed owner, address indexed spender, uint256 value);
```
https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/interfaces/IERC20.sol
```
9	event ApprovalForAll(address indexed owner, address indexed operator, bool approved);
```
https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/interfaces/IERC721.sol
```
15	event ApprovalForAll(address indexed account, address indexed operator, bool approved);
17	event URI(string value, uint256 indexed id);
```
https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/interfaces/IERC1155.sol









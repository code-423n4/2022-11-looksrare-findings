

## The `immutable` and `constant` variables should be written in uppercase.
**Details**: According to the [Solidity](https://docs.soliditylang.org/en/v0.8.10/style-guide.html#constants)[ ](https://docs.soliditylang.org/en/v0.8.10/style-guide.html#constants)[Style](https://docs.soliditylang.org/en/v0.8.10/style-guide.html#constants)[ ](https://docs.soliditylang.org/en/v0.8.10/style-guide.html#constants)[Guide](https://docs.soliditylang.org/en/v0.8.10/style-guide.html#constants)[ ](https://docs.soliditylang.org/en/v0.8.10/style-guide.html#constants)constants should be named with all capital letters with underscores separating words. Immutables can be treated similar to constants because they are replaced by their assigned values during contract construction.
```solidity
ERC20EnabledLooksRareAggregator.sol:16:    ILooksRareAggregator public immutable aggregator;
proxies/LooksRareProxy.sol:30:    ILooksRareExchange public immutable marketplace;
proxies/LooksRareProxy.sol:31:    address public immutable aggregator;
proxies/SeaportProxy.sol:20:    SeaportInterface public immutable marketplace;
proxies/SeaportProxy.sol:21:    address public immutable aggregator;
```

##  Inconsistent function return patterns affects readability
**Summary**: Some functions explicitly return a value while others assign a value to a named return variable.
**Details**: If many functions explicitly return a value, one may incorrectly infer that functions assigning a value to a named return variable are not actually returning anything.
```solidity
LooksRareAggregator.sol:184:    function supportsProxyFunction(address proxy, bytes4 selector) external view returns (bool) {
SignatureChecker.sol:56:    function _recoverEOASigner(bytes32 hash, bytes memory signature) internal pure returns (address signer) {
interfaces/IERC1271.sol:5:    function isValidSignature(bytes32 hash, bytes memory signature) external view returns (bytes4 magicValue);
interfaces/IERC721.sol:11:    function balanceOf(address owner) external view returns (uint256 balance);
interfaces/IERC721.sol:13:    function ownerOf(uint256 tokenId) external view returns (address owner);
interfaces/IERC721.sol:38:    function getApproved(uint256 tokenId) external view returns (address operator);
interfaces/IERC721.sol:40:    function isApprovedForAll(address owner, address operator) external view returns (bool);
interfaces/IWETH.sol:7:    function transfer(address dst, uint256 wad) external returns (bool);
interfaces/SeaportInterface.sol:44:    function fulfillBasicOrder(BasicOrderParameters calldata parameters) external payable returns (bool fulfilled);
interfaces/SeaportInterface.sol:68:    function fulfillOrder(Order calldata order, bytes32 fulfillerConduitKey) external payable returns (bool fulfilled);
interfaces/SeaportInterface.sol:323:    function cancel(OrderComponents[] calldata orders) external returns (bool cancelled);
interfaces/SeaportInterface.sol:340:    function validate(Order[] calldata orders) external returns (bool validated);
interfaces/SeaportInterface.sol:349:    function incrementCounter() external returns (uint256 newCounter);
interfaces/SeaportInterface.sol:358:    function getOrderHash(OrderComponents calldata order) external view returns (bytes32 orderHash);
interfaces/SeaportInterface.sol:394:    function getCounter(address offerer) external view returns (uint256 counter);
interfaces/SeaportInterface.sol:417:    function name() external view returns (string memory contractName);
interfaces/IERC20.sol:9:    function totalSupply() external view returns (uint256);
interfaces/IERC20.sol:11:    function balanceOf(address account) external view returns (uint256);
interfaces/IERC20.sol:13:    function transfer(address to, uint256 amount) external returns (bool);
interfaces/IERC20.sol:15:    function allowance(address owner, address spender) external view returns (uint256);
interfaces/IERC20.sol:17:    function approve(address spender, uint256 amount) external returns (bool);
interfaces/IERC1155.sol:19:    function balanceOf(address account, uint256 id) external view returns (uint256);
interfaces/IERC1155.sol:28:    function isApprovedForAll(address account, address operator) external view returns (bool);
```
**Mitigation**: Consider refactoring functions to standardise on one return pattern, preferably explicit returns.

## Use of `Block.timestamp`
Block timestamps have historically been used for a variety of applications, such as entropy for random numbers (see the Entropy Illusion for further details), locking funds for periods of time, and various state-changing conditional statements that are time-dependent. Miners have the ability to adjust timestamps slightly, which can prove to be dangerous if block timestamps are used incorrectly in smart contracts.
```solidity
OwnableTwoSteps.sol:70:        if (block.timestamp < earliestOwnershipRenouncementTime) revert RenouncementTooEarly();
OwnableTwoSteps.sol:114:        earliestOwnershipRenouncementTime = block.timestamp + delay;
```

##  `address.call{value:x}()` should be used instead of `payable.transfer()`
```solidity
```
## No indexed event parameters
**Summary**: None of Sherlock’s events use indexed parameters. This could make it harder and inefficient for off-chain tools to analyse them.
**Details**: Indexed parameters (“topics”) are searchable event parameters. They are stored separately from unindexed event parameters in an efficient manner to allow for faster access. This is useful for efficient off-chain-analysis, but it is also more costly gas-wise.
```solidity
LooksRareAggregator.sol:38:     *         The purpose is to prevent a malicious proxy from stealing users' ERC20 tokens if
LooksRareAggregator.sol:116:     * @dev Must be called by the current owner. It can only be set once to prevent
poo:16:ReentrancyGuard	14	Prevent re-entrancy attacks	N/A
ReentrancyGuard.sol:19:     * @notice Modifier to wrap functions to prevent reentrancy calls
interfaces/IOwnableTwoSteps.sol:24:    event CancelOwnershipTransfer();
interfaces/IOwnableTwoSteps.sol:25:    event InitiateOwnershipRenouncement(uint256 earliestOwnershipRenouncementTime);
interfaces/IOwnableTwoSteps.sol:26:    event InitiateOwnershipTransfer(address previousOwner, address potentialOwner);
interfaces/IOwnableTwoSteps.sol:27:    event NewOwner(address newOwner);
interfaces/ILooksRareAggregator.sol:36:    event ERC20EnabledLooksRareAggregatorSet();
interfaces/ILooksRareAggregator.sol:44:    event FeeUpdated(address proxy, uint256 bp, address recipient);
interfaces/IERC1155.sol:7:    event TransferBatch(
```





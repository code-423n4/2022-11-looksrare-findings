[L-01] Miss check for 'recipient' in 'setFee()' function
```
function setFee(
    address proxy,
    uint256 bp,
    address recipient // @audit miss check if it's address(0)
) external onlyOwner {
    if (bp > 10000) revert FeeTooHigh();
    _proxyFeeData[proxy].bp = bp;
    _proxyFeeData[proxy].recipient = recipient;

    emit FeeUpdated(proxy, bp, recipient);
}
```
https://github.com/code-423n4/2022-11-looksrare/blob/e3b2c053f722b0ca2dce3a3eb06f64859b8b7a6f/contracts/LooksRareAggregator.sol#L156


[L-02] Miss check for 'to' in 'rescueERC721()' function
```
function rescueERC721(
    address collection,
    address to, // @audit miss check 'to'
    uint256 tokenId
) external onlyOwner {
    _executeERC721TransferFrom(collection, address(this), to, tokenId);
}
```
https://github.com/code-423n4/2022-11-looksrare/blob/e3b2c053f722b0ca2dce3a3eb06f64859b8b7a6f/contracts/LooksRareAggregator.sol#L197


[L-03] Miss check for 'to' in 'rescueERC1155()' function
```
function rescueERC1155(
    address collection,
    address to, // @audit miss check 'to'
    uint256[] calldata tokenIds,
    uint256[] calldata amounts
) external onlyOwner {
    _executeERC1155SafeBatchTransferFrom(collection, address(this), to, tokenIds, amounts);
}
```
https://github.com/code-423n4/2022-11-looksrare/blob/e3b2c053f722b0ca2dce3a3eb06f64859b8b7a6f/contracts/LooksRareAggregator.sol#L213


[L-04] It's recommended to use 'safeTransferFrom' interface to transfer  ERC721 token
```
function _executeERC721TransferFrom(
    address collection,
    address from,
    address to,
    uint256 tokenId
) internal {
    (bool status, ) = collection.call(abi.encodeWithSelector(IERC721.transferFrom.selector, from, to, tokenId)); // @audit not safe
    if (!status) revert ERC721TransferFromFail();
}
```
https://github.com/code-423n4/2022-11-looksrare/blob/e3b2c053f722b0ca2dce3a3eb06f64859b8b7a6f/contracts/lowLevelCallers/LowLevelERC721Transfer.sol#L27
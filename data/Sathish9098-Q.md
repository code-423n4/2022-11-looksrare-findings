1)                 For all solidity files, license keyword should be mentioned as   SPDX-License-Identifier: UNLICENSED.

----------------------------------------------------------------------------------------------------------------------------------

2)              FOR INTERFACE FILES SHOULD USE FIXED COMPILER VERSIONS, NOT FLOATING ONES

     2022-11-looksrare/contracts/interfaces/IERC1155.sol

2:   pragma solidity ^0.8.0;

2022-11-looksrare/contracts/interfaces/IERC1271.sol

2:   pragma solidity ^0.8.0;

2022-11-looksrare/contracts/interfaces/IERC20.sol

2:  pragma solidity ^0.8.0;

2022-11-looksrare/contracts/interfaces/IERC721.sol

2:   pragma solidity ^0.8.0;

2022-11-looksrare/contracts/interfaces/IOwnableTwoSteps.sol

2:   pragma solidity ^0.8.14;

2022-11-looksrare/contracts/interfaces/IReentrancyGuard.sol

2:  pragma solidity ^0.8.0;

2022-11-looksrare/contracts/interfaces/ISignatureChecker.sol

2:   pragma solidity ^0.8.0;

2022-11-looksrare/contracts/interfaces/IWETH.sol

2:   pragma solidity >=0.5.0;

2022-11-looksrare/contracts/interfaces/SeaportInterface.sol

2:   pragma solidity 0.8.17;

----------------------------------------------------------------------------------------------------------------------

3)              SOLIDITY PRAGMA VERSIONING SHOULD BE EXACTLY SAME FOR ALL CONTRACTS . SOME INTERFACES USING 0.8.0 BUT SOME INTERFACES USING 0.8.14 AND 0.8.17 

---------------------------------------------------------------------------------------------------------------------------------------------------

4)                  IN execute  FUNCTION originator IS ONE OF THE  PARAMETER . SO EVERY TIME WHEN WE CALL execute FUNCTION WE ALWAYS SEND THE originator ADDRESS ALONG WITH FUNCTION CALL . INSIDE THE FUNCTION DEFINITION msg.sender ADDRESS IS SET TO originator . IN THIS CASE THE originator PARAMETER IS NOT NEEDED. THE originator PARAMETER VALUE IS NOT USED . 

PROOF OF WORK:  

2022-11-looksrare/contracts/LooksRareAggregator.sol

function execute(
        TokenTransfer[] calldata tokenTransfers,
        TradeData[] calldata tradeData,
        address originator,                                                                          @AUDIT THE originator PARAMETER 
        address recipient,
        bool isAtomic
    ) external payable nonReentrant {
        if (recipient == address(0)) revert ZeroAddress();
        uint256 tradeDataLength = tradeData.length;
        if (tradeDataLength == 0) revert InvalidOrderLength();

        uint256 tokenTransfersLength = tokenTransfers.length;
        if (tokenTransfersLength == 0) {
            originator = msg.sender;                                                                       //@AUDIT msg.sender IS SET AS originator 
        } else if (msg.sender != erc20EnabledLooksRareAggregator) {
            revert UseERC20EnabledLooksRareAggregator();
        }

        for (uint256 i; i < tradeDataLength; ) {
            TradeData calldata singleTradeData = tradeData[i];
            if (!_proxyFunctionSelectors[singleTradeData.proxy][singleTradeData.selector]) revert InvalidFunction();

            (bytes memory proxyCalldata, bool maxFeeBpViolated) = _encodeCalldataAndValidateFeeBp(
                singleTradeData,
                recipient,
                isAtomic
            );
            if (maxFeeBpViolated) {
                if (isAtomic) {
                    revert FeeTooHigh();
                } else {
                    unchecked {
                        ++i;
                    }
                    continue;
                }
            }
            (bool success, bytes memory returnData) = singleTradeData.proxy.delegatecall(proxyCalldata);

            if (!success) {
                if (isAtomic) {
                    if (returnData.length > 0) {
                        assembly {
                            let returnDataSize := mload(returnData)
                            revert(add(32, returnData), returnDataSize)
                        }
                    } else {
                        revert TradeExecutionFailed();
                    }
                }
            }

            unchecked {
                ++i;
            }
        }

        if (tokenTransfersLength > 0) _returnERC20TokensIfAny(tokenTransfers, originator);
        _returnETHIfAny(originator);

        emit Sweep(originator);
    }

 MITIGATION STEPS : 

We can declare originator as local address varibale. THERE IS NO USE GETTING originator PARAMETER VALUE 

----------------------------------------------------------------------------------------------------------------------------------

5)                  It is bad practice to use numbers directly in code without explanation. 

2022-11-looksrare/contracts/LooksRareAggregator.sol

 158:    if (bp > 10000) revert FeeTooHigh();

2022-11-looksrare/contracts/proxies/SeaportProxy.sol

147:      uint256 orderFee = (orders[i].price * feeBp) / 10000;

-------------------------------------------------------------------------------------------------------------
6)              USUAL SUSPECTS:  LACK OF ZERO CHECKS FOR NEW ADDRESSES . IN EVERY FUNCTIONS THE ADDRESS PARAMETERS MUST BE CHECKED FOR ZERO ADDRESS . THE GIVEN PARAMETER VALUE IS NOT EQUAL TO  address(0)

2022-11-looksrare/contracts/LooksRareAggregator.sol

 function approve(
        address marketplace,
        address currency,
        uint256 amount
    ) external onlyOwner {
        _executeERC20Approve(currency, marketplace, amount);
    }

function rescueERC721(
        address collection,
        address to,
        uint256 tokenId
    ) external onlyOwner {
        _executeERC721TransferFrom(collection, address(this), to, tokenId);
    }

---------------------------------------------------------------------------------------------------------------------------------------------------

7)            EVENT IS MISSING INDEXED FIELDS

2022-11-looksrare/contracts/interfaces/IOwnableTwoSteps.sol

 26:    event InitiateOwnershipTransfer(address previousOwner, address potentialOwner);

27)   event NewOwner(address newOwner);


---------------------------------------------------------------------------------------------------------------------------

8)                  Shorter inheritance list

The inheritance contracts on line 26-32 can be consolidated into a shorter list:

2022-11-looksrare/contracts/LooksRareAggregator.sol

contract LooksRareAggregator is
    ILooksRareAggregator,
    TokenRescuer,
    TokenReceiver,
    ReentrancyGuard,
    LowLevelERC20Approve,
    LowLevelERC721Transfer,
    LowLevelERC1155Transfer
{


---------------------------------------------------------------------------------------------------------------------------

9)                 IN CONSTRUCTOR BEFORE ASSIGNING _aggregator MUST BE CHECKED ZERO ADDRESS . ITS POSSIBLE TO PASS ZERO ADDRESS VIA CONSTRUCTOR

2022-11-looksrare/contracts/ERC20EnabledLooksRareAggregator.sol

 constructor(address _aggregator) {
        aggregator = ILooksRareAggregator(_aggregator);
    }

2022-11-looksrare/contracts/proxies/LooksRareProxy.sol


constructor(address _marketplace, address _aggregator) {
        marketplace = ILooksRareExchange(_marketplace);
        aggregator = _aggregator;
    }

2022-11-looksrare/contracts/proxies/SeaportProxy.sol

constructor(address _marketplace, address _aggregator) {
        marketplace = SeaportInterface(_marketplace);
        aggregator = _aggregator;
    }

-------------------------------------------------------------------------------------------------------------------------------------------------------

10)           NOT USING THE NAMED RETURN VARIABLES ANYWHERE IN THE FUNCTION IS CONFUSING

    changing the variable to be an unnamed one

There are 2 instance for this issue : 

FILE:   2022-11-looksrare/contracts/LooksRareAggregator.sol

function _encodeCalldataAndValidateFeeBp(
        TradeData calldata singleTradeData,
        address recipient,
        bool isAtomic
    ) private view returns (bytes memory proxyCalldata, bool maxFeeBpViolated) {               //AUDIT   bytes memory proxyCalldata, bool maxFeeBpViolated
        FeeData memory feeData = _proxyFeeData[singleTradeData.proxy];
        maxFeeBpViolated = singleTradeData.maxFeeBp < feeData.bp;
        proxyCalldata = abi.encodeWithSelector(
            singleTradeData.selector,
            singleTradeData.orders,
            singleTradeData.ordersExtraData,
            singleTradeData.extraData,
            recipient,
            isAtomic,
            feeData.bp,
            feeData.recipient
        );
    }


2022-11-looksrare/contracts/proxies/SeaportProxy.sol

function _populateParameters(BasicOrder calldata order, OrderExtraData memory orderExtraData)
        private
        pure
        returns (OrderParameters memory parameters)                                       ///@AUDIT  parameters
    {
        uint256 recipientsLength = orderExtraData.recipients.length;

        parameters.offerer = order.signer;
        parameters.zone = orderExtraData.zone;
        parameters.zoneHash = orderExtraData.zoneHash;
        parameters.salt = orderExtraData.salt;
        parameters.conduitKey = orderExtraData.conduitKey;
        parameters.orderType = orderExtraData.orderType;
        parameters.startTime = order.startTime;
        parameters.endTime = order.endTime;
        parameters.totalOriginalConsiderationItems = recipientsLength;

        OfferItem[] memory offer = new OfferItem[](1);
        // Seaport enums start with NATIVE and ERC20 so plus 2
        offer[0].itemType = ItemType(uint8(order.collectionType) + 2);
        offer[0].token = order.collection;
        offer[0].identifierOrCriteria = order.tokenIds[0];
        uint256 amount = order.amounts[0];
        offer[0].startAmount = amount;
        offer[0].endAmount = amount;
        parameters.offer = offer;

        ConsiderationItem[] memory consideration = new ConsiderationItem[](recipientsLength);
        for (uint256 j; j < recipientsLength; ) {
            // We don't need to assign value to identifierOrCriteria as it is always 0.
            uint256 recipientAmount = orderExtraData.recipients[j].amount;
            consideration[j].startAmount = recipientAmount;
            consideration[j].endAmount = recipientAmount;
            consideration[j].recipient = payable(orderExtraData.recipients[j].recipient);
            consideration[j].itemType = order.currency == address(0) ? ItemType.NATIVE : ItemType.ERC20;
            consideration[j].token = order.currency;

            unchecked {
                ++j;
            }
        }
        parameters.consideration = consideration;
    }
}
--------------------------------------------------------------------------------------------------------------------------



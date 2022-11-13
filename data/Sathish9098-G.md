2)    FUNCTIONS GUARANTEED TO REVERT WHEN CALLED BY NORMAL USERS CAN BE MARKED AS payable
   
  If a function is  modifier such as "onlyOwner" is used, the function will revert if the normal user tries to pay the function. Marking the function as payable will lower the gas cost for legitimate callers because the compiler will not include checks for whether a payment was provided. The extra opcodes avoided are CALLVALUE(2),DUP1(3),ISZERO(3),PUSH2(3),JUMPI(10),PUSH1(3),DUP1(3),REVERT(0),JUMPDEST(1),POP(2), which costs an average of about 21 gas per call to the function, in addition to the extra deployment cost.

There are 4 instances of this issue.

FILE: 2022-11-looksrare/contracts/LooksRareAggregator.sol

120:         function setERC20EnabledLooksRareAggregator(address _erc20EnabledLooksRareAggregator) external onlyOwner  {

132:         function addFunction(address proxy, bytes4 selector) external onlyOwner {

144:         function removeFunction(address proxy, bytes4 selector) external onlyOwner {


https://github.com/code-423n4/2022-11-looksrare/blob/e3b2c053f722b0ca2dce3a3eb06f64859b8b7a6f/contracts/LooksRareAggregator.sol#L153-L157

https://github.com/code-423n4/2022-11-looksrare/blob/e3b2c053f722b0ca2dce3a3eb06f64859b8b7a6f/contracts/LooksRareAggregator.sol#L171-L175

-----------------------------------------------------------------------------------------------------------------------------------------------------------
3)            OPTIMIZE NAMES TO SAVE GAS

 Can optimize the functions to avoid the more gas fee. We can replace with very short function names  .public/external function names and public member variable names can be optimized to save gas.Below are the interfaces/abstract contracts that can be optimized so that the most frequently-called functions use the least amount of gas possible during method lookup. 

There are 5 instances of this issue.

FILE:  2022-11-looksrare/contracts/interfaces/SeaportInterface.sol

///@audit  fulfillBasicOrder
 function fulfillBasicOrder(BasicOrderParameters calldata parameters) external payable returns (bool fulfilled);

///@audit  fulfillAdvancedOrder
  function fulfillAdvancedOrder(
        AdvancedOrder calldata advancedOrder,
        CriteriaResolver[] calldata criteriaResolvers,
        bytes32 fulfillerConduitKey,
        address recipient
    ) external payable returns (bool fulfilled);

@audit  fulfillAvailableOrders
 function fulfillAvailableOrders(Order[] calldata orders,
        FulfillmentComponent[][] calldata offerFulfillments,
        FulfillmentComponent[][] calldata considerationFulfillments,
        bytes32 fulfillerConduitKey,
        uint256 maximumFulfilled
    ) external payable returns (bool[] memory availableOrders, Execution[] memory executions);

@audit fulfillAvailableAdvancedOrders
 function fulfillAvailableAdvancedOrders(
        AdvancedOrder[] calldata advancedOrders,
        CriteriaResolver[] calldata criteriaResolvers,
        FulfillmentComponent[][] calldata offerFulfillments,
        FulfillmentComponent[][] calldata considerationFulfillments,
        bytes32 fulfillerConduitKey,
        address recipient,
        uint256 maximumFulfilled
    ) external payable returns (bool[] memory availableOrders, Execution[] memory executions);

---------------------------------------------------------------------------------------------------------------------------------------------

4)  FOR || OPERATOR DON'T WANT TO CHECK ALL CONDITIONS IN  if (ordersLength == 0 || ordersLength != ordersExtraData.length). IN THIS IF ordersLength == 0 IS TRUE THEN THE OVER ALL if CONDITION BECOME TRUE . THEN ordersLength != ordersExtraData.length IS NOT NECESSORY TO CHECK . WE CAN SKIP SECOND CONDITION CHECK .

Every skipped  condition check we can save 28 gas .

There are 2 instances of this issue.

FILE:    2022-11-looksrare/contracts/proxies/SeaportProxy.sol

72:        if (ordersLength == 0 || ordersLength != ordersExtraData.length) revert InvalidOrderLength();

FILE:   2022-11-looksrare/contracts/proxies/LooksRareProxy.sol

62:      if (ordersLength == 0 || ordersLength != ordersExtraData.length) revert InvalidOrderLength();

-----------------------------------------------------------------------------------------------------------------------------------------------------------

5)          <X> += <Y> COSTS MORE GAS THAN <X> = <X> + <Y> FOR VARIABLES

There are 3 instances of this issue.

FILE:  2022-11-looksrare/contracts/proxies/SeaportProxy.sol

109:       if (orders[i].currency == address(0)) ethValue += orders[i].price;

150:       fee += orderFee;  

209:       fee += orderFee;    











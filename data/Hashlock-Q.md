Low: Payable functions in ERC20EnabledLooksRareAggregator.sol
This contract shouldnt have payable functions as it shouldnt receive ether. 
Location: ERC20EnabledLooksRareAggregator.sol Line 33.
Impact: Funds (ether) potentially being directed to the wrong place.
Recommendation: This function does not need to be payable. 

Low: Lack of address (0x0) best practice check, and lack of check that the input is the correct address, with inability to edit the address.
Location: Line 120 of LooksRareAggregator.sol. 
Impact: The owner could put in a typo, setting the address to the wrong one or address 0. Is it possible to make this an internal function only called from ERC20EnabledLooksRareAggregator.sol with its own address? Mistakes could result in DOS and need to re-deploy. 
The contract also does not allow for such mistakes to be changed as it can only be called once if the input was not address(0). 
Recommendation: after the if statement, require address != 0.
Create an onlyowner function to update the address later, unless this can be set directly by the _erc20EnabledLooksRareAggregator.sol contract with its own address. 

Low: ERC20EnabledLooksRareAggregator.sol can be forced to accept eth via selfdestruct()
Impact: Funds (ether) potentially being directed to the wrong place. This contract shouldnt receive ether. 

Low: No address check on constructor
Location: Line 21 of ERC20EnabledLooksRareAggregator.sol
Impact: Potential for initial address of aggregator to be incorrect. There is no way or function to change it after the constructor. 

Low: Unused receive function
Should call something else or revert. In LooksRareAggregator.sol and Seaport proxy. 
Impact: Funds (ether) potentially being directed to the wrong place.
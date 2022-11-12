QA1 round up fee calculation
the fee is calculated as such:
```
uint256 orderFee = (orders[i].price * feeBp) / 10000; 
```
when the last 4 digits of orders[i].price * feeBp is greater than 5000, say 9999, dividing by 10000 rounds it down to 0. 
Instead, the calculation can be changed to 
```
uint256 orderFee = (orders[i].price * feeBp + 5000) / 10000;
```
This way fees are rounded more properly.
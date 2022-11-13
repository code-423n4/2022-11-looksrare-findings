### QA1:  
ERC20EnabledLooksRareAggregator contract does not inherit TokenRescuer, it should be inherited to prevent unpredictable tokens from being trapped in the contract

detail: https://github.com/code-423n4/2022-11-looksrare/blob/e3b2c053f722b0ca2dce3a3eb06f64859b8b7a6f/contracts/ERC20EnabledLooksRareAggregator.sol#L15

### QA2:  

The parameters should be checked more safely. The endTime of struct BasicOrder should not be less than startTime.

![image](https://user-images.githubusercontent.com/116964767/201529237-d916cb9a-d72e-4daf-92b2-c706a9248326.png)
![image](https://user-images.githubusercontent.com/116964767/201529356-7c1c7a24-6a43-4637-a042-843ff7a56fb6.png)

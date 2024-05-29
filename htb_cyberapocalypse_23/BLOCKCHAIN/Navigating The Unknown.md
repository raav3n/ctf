# Navigating the Unknown (very easy)
## Description 
Your advanced sensory systems make it easy for you to navigate familiar environments, but you must rely on intuition to navigate in unknown territories. Through practice and training, you must learn to read subtle cues and become comfortable in unpredictable situations. Can you use your software to find your way through the blocks?

## Initial Thoughts
I have no idea what is going on. After reading the README it seems like we are presented with two ports, one is a tcp port and the other is rpc. I would have to connect to one of these port and call a function on a smart contract.

## Port Breakdown
TCP: this is a commonly known port for transmitting data, when connecting to it I got a menu with 3 options.
```
1 - Connection information
2 - Restart Instance
3 - Get flag
```
Option 1 gave me the following information:

Private key  
Address        
Target contract 
Setup contract  

I save this for later. 

RPC: I wasn't too familiar with this port but seems like its commonly used to communicate amongst machines. Anyhow I figured this was the one I needed to use for the smart contracts.

## Learning curve 
At this point I tried to learn a little more on how to use web3py since the README suggested it. I reached out to my trusty friend chatgpt and it gave me a good starting point and it looked like I only needed the following to call a function.

- our rpc ip/port
- the contract's address
- contract abi (I'll get to that) 
	- gas (is included in the abi but didnt seem like we needed it for this)
- function name
- function arguments
- none (its simply just some random number blockchain uses for a block, means 'number only used once')

from there just signed it and sent the transaction.

### Contract abi?
In short the abi is pretty much like rules needed by web3py that tells it how to exactly interact with the contract and how to interpret the data. Without it, it's clueless. 

This was not given so I used https://remix.ethereum.org/ (an online Solidity compiler). All I had to do was upload the Unknonwn contract file, run it and in the Unknown_metadata.json file it created I saw the abi.
```
"abi": [
			{
				"inputs": [
					{
						"internalType": "uint256",
						"name": "version",
						"type": "uint256"
					}
				],
				"name": "updateSensors",
				"outputs": [],
				"stateMutability": "nonpayable",
				"type": "function"
			},
			{
				"inputs": [],
				"name": "updated",
				"outputs": [
					{
						"internalType": "bool",
						"name": "",
						"type": "bool"
					}
				],
				"stateMutability": "view",
				"type": "function"
			}
		],
```

## Unknown Call
One of the files given is Unknown.sol this is the smart contract we are told to call. It had one function updateSensors which set updated = true when called on if the argument 'version' was 10.

Looking at Setup.sol we also knew that if updated was set to true then we would be able to get our flag from that tcp port.
```
function isSolved() public view returns (bool) {
        return TARGET.updated();
    }
```

## Web3py Code
Below is the code I used for this challenge.
```
from web3 import Web3
import json

# connect to an Ethereum node
w3 = Web3(Web3.HTTPProvider(ip:port))

# set up the contract
contract_address = '0x42....'
contract_abi = [
			{
				"inputs": [
					{
						"internalType": "uint256",
						"name": "version",
						"type": "uint256"
					}
				],
				"name": "updateSensors",
				"outputs": [],
				"stateMutability": "nonpayable",
				"type": "function"
			},
			{
				"inputs": [],
				"name": "updated",
				"outputs": [
					{
						"internalType": "bool",
						"name": "",
						"type": "bool"
					}
				],
				"stateMutability": "view",
				"type": "function"
			}
		]
#establish contract 
contract = w3.eth.contract(address=contract_address, abi=contract_abi)

# build the transaction
function_name = 'updateSensors'
function_args = [10] #we want this set to set so that we can set updated to true 
nonce = w3.eth.get_transaction_count(address) #our address

transaction = contract.functions[function_name](*function_args).build_transaction({
    'from': address, #our address
    'nonce': nonce,
})

# sign the transaction
signed_txn = w3.eth.account.sign_transaction(transaction, private_key) #our private key

# send the transaction
tx_hash = w3.eth.send_raw_transaction(signed_txn.rawTransaction)
```

With that I got the flag.

## Summary
I had to call the updateSensors function for the Unknown smart contract with the argument 10 so that the variable 'updated' gets set to true making the Setup contract return true for the isSolved function that gets validated when attempted to get the flag via the tcp port.
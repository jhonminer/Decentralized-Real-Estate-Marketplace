# Decentralized-Real-Estate-Marketplace
Build a WEB3-based real estate marketplace that streamlines property transactions and reduces fees through smart contracts.
from web3 import Web3, HTTPProvider
from solcx import compile_standard, install_solc
import json

# Ensure you have the correct version of the Solidity compiler
install_solc('0.8.0')

# Connect to an Ethereum testnet or local network
w3 = Web3(HTTPProvider('http://127.0.0.1:8545'))
w3.eth.default_account = w3.eth.accounts[0]  # Use the first account

# Solidity source code (you'd replace the following with the actual Solidity code as a string)
solidity_source_code = '''
// Your Solidity code here
'''

compiled_sol = compile_standard({
    "language": "Solidity",
    "sources": {"RealEstateMarketplace.sol": {"content": solidity_source_code}},
    "settings": {
        "outputSelection": {
            "*": {
                "*": ["metadata", "evm.bytecode", "evm.bytecode.sourceMap"]
            }
        }
    }
}, solc_version='0.8.0')

bytecode = compiled_sol['contracts']['RealEstateMarketplace.sol']['RealEstateMarketplace']['evm']['bytecode']['object']
abi = json.loads(compiled_sol['contracts']['RealEstateMarketplace.sol']['RealEstateMarketplace']['metadata'])['output']['abi']

# Deploying the contract
RealEstateMarketplace = w3.eth.contract(abi=abi, bytecode=bytecode)
tx_hash = RealEstateMarketplace.constructor().transact()
tx_receipt = w3.eth.wait_for_transaction_receipt(tx_hash)
contract = w3.eth.contract(address=tx_receipt.contractAddress, abi=abi)

# Example usage: listing a new property
tx_hash = contract.functions.listProperty("123 Main St", w3.toWei(1, 'ether')).transact()
w3.eth.wait_for_transaction_receipt(tx_hash)

# Fetching listed properties
properties = contract.functions.properties(0).call()
print(properties)

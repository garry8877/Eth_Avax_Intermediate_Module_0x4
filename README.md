# Degen Gaming ERC20 Token - Metacrafters Module 4 Building On Avalanche

## Overview

The **Degen Gaming** smart contract is an ERC20 token deployed on the **Avalanche Fuji Testnet**. It serves as the utility token for the **Degen Gaming** platform, where users can perform the following actions:  
- Mint tokens  
- Transfer tokens  
- Burn tokens  
- Redeem in-game items from the game store  

This token has additional functionalities such as an in-game store for item redemption, with various items preloaded in the smart contract.

---

## Features & Smart Cntract

```solidity
/*Your task is to create a ERC20 token and deploy it on the Avalanche network 
for Degen Gaming. The smart contract should have the following functionality:
*/

// SPDX-License-Identifier: MIT
pragma solidity ^0.8.18;

contract DegenGaming {

    // State Variable 

    address public owner;
    string public name = "Degen Gaming";
    string public symbol = "Degen";
    uint8 public decimals = 10;
    uint256 public totalSupply = 0;

    // Mapping

    mapping(uint256 => string) public ItemName;
    mapping(uint256 => uint256) public Itemprice;
    mapping(address => uint256) public balance;
    mapping(address => mapping(uint256 => bool)) public redeemedItems;
    mapping(address => uint256) public redeemedItemCount;

    // Use 'Constructor' to initialize values

    constructor() {
        owner = msg.sender;

        // Initialize some sample items in the store
        GameStore(0, "Metacrafters Sword", 500);
        GameStore(1, "Metacrafters Hoddies", 1000);
        GameStore(2, "Metacrafters Gun", 20000);
        GameStore(3, "Blockchain Destroyer", 25000);
    }

    // Modifier to Control Access

    modifier onlyOwner() {
        require(msg.sender == owner, "This function can only be used by the owner.");
        _;
    }

    // Taking Record of Transactions

    event Mint(address indexed to, uint256 value);
    event Transfer(address indexed from, address indexed to, uint256 value);
    event Burn(address indexed from, uint256 value);
    event Redeem(address indexed user, string itemName);

    
    // Store Function 

    function GameStore(uint256 itemId, string memory _itemName, uint256 _itemPrice) public onlyOwner {
        ItemName[itemId] = _itemName;
        Itemprice[itemId] = _itemPrice;
    }

    // Mint Functions

    function mint(address to, uint256 amount) external onlyOwner {
        totalSupply += amount;
        balance[to] += amount;
        emit Mint(to, amount);
        emit Transfer(address(0), to, amount);
    }
    
    // Get Balance

    function balanceOf(address accountAddress) external view returns (uint256) {
        return balance[accountAddress];
    }

    // Transfer Token

    function transfer(address receiver, uint256 amount) external returns (bool) {
        require(balance[msg.sender] >= amount, "Insufficient balance.");
        balance[msg.sender] -= amount;
        balance[receiver] += amount;
        emit Transfer(msg.sender, receiver, amount);
        return true;
    }

    // Burn Function 

    function burn(uint256 amount) external {
        require(amount <= balance[msg.sender], "Insufficient balance.");
        balance[msg.sender] -= amount;
        totalSupply -= amount;
        emit Burn(msg.sender, amount);
        emit Transfer(msg.sender, address(0), amount);
    }

    // Redeem Item from store

    function Itemredeem(uint256 accId) external returns (string memory) {
        require(Itemprice[accId] > 0, "Invalid item ID.");
        uint256 redemptionAmount = Itemprice[accId];
        require(balance[msg.sender] >= redemptionAmount, "Insufficient balance to redeem the item.");

        balance[msg.sender] -= redemptionAmount;
        redeemedItems[msg.sender][accId] = true;
        redeemedItemCount[msg.sender]++;
        emit Redeem(msg.sender, ItemName[accId]);

        return ItemName[accId];
    }

    // Get Redeemed Item Information

    function getRedeemedItemCount(address user) external view returns (uint256) {
        return redeemedItemCount[user];
    }
}
```

### Token Details
- **Name:** Degen Gaming  
- **Symbol:** Degen  
- **Decimals:** 10  
- **Total Supply:** Initially set to 0 (can be minted by the owner).  

### Key Functionalities
1. **Mint Tokens:**  
   Only the owner can mint tokens and assign them to an address.  
2. **Transfer Tokens:**  
   Users can transfer tokens to other addresses.  
3. **Burn Tokens:**  
   Users can burn their tokens to decrease the total supply.  
4. **Redeem In-Game Items:**  
   Users can use tokens to redeem preloaded items from the game store.  
5. **Get Redeemed Items Count:**  
   Users can check the number of items they have redeemed.

### Access Control
- The owner (contract deployer) has exclusive rights to mint tokens and manage the game store.

---

## Preloaded Store Items

| Item ID | Item Name               | Price (Degen) |
|---------|--------------------------|---------------|
| 0       | Metacrafters Sword      | 500           |
| 1       | Metacrafters Hoodies    | 1,000         |
| 2       | Metacrafters Gun        | 20,000        |
| 3       | Blockchain Destroyer    | 25,000        |

---

## Deployment Instructions

### Requirements
1. **Remix IDE:** Use Remix IDE for compiling and deploying the contract.
2. **Avalanche Fuji Testnet Wallet:** Ensure you have an Avalanche-compatible wallet (e.g., MetaMask) configured for the Fuji Testnet.
3. **Testnet AVAX:** Fund your wallet with testnet AVAX for gas fees. You can get it from the [Avalanche Fuji Faucet](https://faucet.avax.network/).

### Steps
1. Open [Remix IDE](https://remix.ethereum.org/).
2. Create a new Solidity file (e.g., `DegenGaming.sol`) and paste the contract code.
3. Compile the contract using the Solidity compiler (set to version `0.8.18`).
4. Deploy the contract:  
   - Select the **Injected Web3** environment in Remix.  
   - Connect your MetaMask wallet configured for Avalanche Fuji Testnet.  
   - Deploy the contract.  

---

## Interaction with the Contract

### Using Remix
1. **Mint Tokens:**  
   Call the `mint` function with the recipient's address and the amount of tokens.  
2. **Transfer Tokens:**  
   Call the `transfer` function with the recipient's address and the token amount.  
3. **Burn Tokens:**  
   Call the `burn` function with the amount to burn.  
4. **Redeem Items:**  
   Call the `Itemredeem` function with the item ID to redeem the corresponding item.  
5. **Check Balance:**  
   Use the `balanceOf` function with the address to get the token balance.

### Testing on Avalanche Fuji
To test the functionalities, use the **Avalanche Fuji Testnet**. Monitor transactions and balances using tools like [Snowtrace](https://testnet.snowtrace.io/).

---

## Events

The contract emits the following events for better traceability:
- **Mint:** When new tokens are minted.  
- **Transfer:** When tokens are transferred.  
- **Burn:** When tokens are burned.  
- **Redeem:** When an item is redeemed from the store.

---

## Future Enhancements
- Add dynamic item management (add/remove items after deployment).  
- Implement staking mechanisms.  
- Deploy the contract on Avalanche Mainnet.  

---

## License
This project is licensed under the **MIT License**.  

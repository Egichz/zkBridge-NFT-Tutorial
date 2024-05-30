# zkBridge-NFT-Tutorial
Comprehensive tutorial on implementing cross-chain NFT transfers using zkBridge from Polyhedra Network
markdown
Копировать код
# zkBridge-NFT-Tutorial

## Comprehensive Tutorial: Implementing Cross-Chain NFT Transfers with zkBridge

### Introduction

In this tutorial, we will explore how to use the zkBridge protocol from Polyhedra Network to perform secure and efficient cross-chain NFT transfers. zkBridge leverages zkSNARKs to facilitate trusted interactions between different blockchain networks, enabling the transfer of unique digital assets without compromising security.

### Step 1: Setting Up the Environment

To get started, you will need the following tools and libraries:
- Remix - Ethereum IDE
- MetaMask
- Node.js and npm
- Web3.js library

### Step 2: Deploying the ERC-721 Contract on the Sender Chain

First, we will create and deploy a standard ERC-721 contract. Use Remix - Ethereum IDE for this task.

**Sample ERC-721 Contract:**
```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.0;

import "@openzeppelin/contracts/token/ERC721/ERC721.sol";
import "@openzeppelin/contracts/utils/Counters.sol";
import "@openzeppelin/contracts/access/Ownable.sol";

contract SampleERC721 is ERC721, Ownable {
    using Counters for Counters.Counter;
    Counters.Counter private _tokenIds;
    string private _baseTokenURI;

    constructor(string memory name, string memory symbol) ERC721(name, symbol) {}

    function mint() public {
        _safeMint(msg.sender, _tokenIds.current());
        _tokenIds.increment();
    }

    function _baseURI() internal view virtual override returns (string memory) {
        return _baseTokenURI;
    }

    function setBaseURI(string calldata baseURI) external onlyOwner {
        _baseTokenURI = baseURI;
    }
}
Deploy the Contract:
Open Remix - Ethereum IDE.
Create a new file and paste the above contract code.
Compile the contract and deploy it on a test Ethereum network, such as Rinkeby or Goerli.
Step 3: Minting an NFT
Now that the contract is deployed, we can mint an NFT.

Mint the NFT:
In Remix, select the deployed contract.
Find the mint() function and call it to mint a new NFT.
Step 4: Initiating the Cross-Chain Transfer
We will use zkBridge to initiate the transfer. We need to package the NFT transfer details into a payload and send it.

Example Function to Initiate Transfer:

solidity
Копировать код
function initiateTransfer(address nftContract, uint256 tokenId, address recipient, uint16 dstChainId) external {
    // Encode transfer details
    bytes memory payload = abi.encode(nftContract, tokenId, recipient, dstChainId);
    // Call zkBridge send function
    zkBridge.send(payload);
}
Encode Transfer Details:

Ensure all details (NFT contract address, token ID, recipient address, and destination chain ID) are encoded into the payload.
Initiate the Transfer:

Call the initiateTransfer function with the appropriate parameters.
Step 5: Generating and Verifying zkProof
The block header relay network generates a zkProof for the sender chain’s block header, which is then verified by the updater contract on the receiver chain.

Generate zkProof:

The block header relay network generates proof of the block header's correctness.
Verify zkProof:

The updater contract on the receiver chain verifies the zkProof and updates the client state accordingly.
Step 6: Receiving and Processing the NFT on the Receiver Chain
On the receiver chain, use the zkBridge interface to receive and process the NFT transfer.

Example Function to Receive and Process:

solidity
Копировать код
function zkReceive(uint16 srcChainId, bytes32 srcBlockHash, bytes calldata zkProof) external {
    // Decode payload
    (address nftContract, uint256 tokenId, address recipient) = abi.decode(payload, (address, uint256, address));
    // Mint or transfer NFT on receiver chain
    INFTContract(nftContract).mint(recipient, tokenId);
}
Receive zkProof:

Call the zkReceive function with the parameters srcChainId, srcBlockHash, and zkProof.
Complete the Transfer:

The contract decodes the payload and performs the minting or transfer of the NFT on the receiver chain.
Conclusion
We have covered the process of implementing cross-chain NFT transfers using zkBridge. This process includes deploying smart contracts, minting NFTs, initiating and verifying the transfer, and finalizing the transfer on the receiver chain. These steps ensure secure and efficient cross-chain transfers of digital assets.

# Registry Contract Documentation

## Introduction

The **[Registry Contract](https://github.com/horuslabsio/TBA/blob/v3/README.md#the-registry-contract)** is a key part of the Tokenbound system, enabling the deployment and management of Tokenbound Accounts (TBA) associated with NFTs. It allows for the deterministic calculation and deployment of Tokenbound Account addresses using the [ERC-6551](https://github.com/erc6551/reference) standard. This contract provides mechanisms to:

- Deploy Tokenbound Accounts based on an NFT's contract address and token ID.
- Calculate the address of a Tokenbound Account for a specific NFT without deploying it.
- Ensure that only the NFT's owner can create an account.

### Event Logging

The Registry contract also logs events when a Tokenbound Account is successfully created, allowing for transparency and easy tracking of deployments.

## Functions

### 1. `create_account`

```cairo
fn create_account(
    ref self: ContractState,
    implementation_hash: felt252,
    token_contract: ContractAddress,
    token_id: u256,
    salt: felt252,
    chain_id: felt252
) -> ContractAddress
```

### Description:

This function deploys a new Tokenbound Account for an NFT. It requires that the caller be the owner of the NFT in question, ensuring that only authorized users can create Tokenbound Accounts.

### Parameters (type):

- `implementation_hash (felt252)`: The class hash of the reference account (implementation contract).
- `token_contract (ContractAddress)`: The contract address of the NFT.
- `token_id (u256)`: The unique ID of the NFT within the contract.
- `salt (felt252)`: A random value used for the deterministic address calculation.
- `chain_id (felt252)`: The ID of the blockchain network.

### Returns:

- `account_address (ContractAddress)`: The deployed Tokenbound Account address for the provided NFT.

### Emits:

- `AccountCreated`: Emitted when a new Tokenbound Account is successfully deployed.

### 2. get_account

```cairo
fn get_account(
    self: @ContractState,
    implementation_hash: felt252,
    token_contract: ContractAddress,
    token_id: u256,
    salt: felt252,
    chain_id: felt252
) -> ContractAddress
```

### Description:

This function calculates the address of a Tokenbound Account without deploying it. The account address is calculated deterministically using the provided contract address, token ID, and other parameters, meaning that the address will be the same each time for a given set of inputs.

### Parameters (type):

- `implementation_hash (felt252)`: The class hash of the reference account.
- `token_contract (ContractAddress)`: The contract address of the NFT.
- `token_id (u256)`: The unique ID of the NFT.
- `salt (felt252)`: A random value used for the deterministic address calculation.
- `chain_id (felt252)`: The ID of the blockchain network.

### Returns:

- account_address (ContractAddress): The calculated Tokenbound Account address for the provided NFT and parameters.

## Private Functions

### 1. `_get_owner`

```cairo
fn _get_owner(
    self: @ContractState,
    token_contract: ContractAddress,
    token_id: u256
) -> ContractAddress
```

### Description:

This internal function retrieves the owner of an NFT from its contract. It first tries to use the CamelCase ownerOf function and falls back to the snake_case owner_of function for compatibility with various NFT contracts.

### Parameters (type):

- `token_contract (ContractAddress)`: The contract address of the NFT.
- `token_id (u256)`: The unique identifier of the NFT within the contract.

### Returns:

- `owner (ContractAddress)`: The address of the NFT owner.

## Events

1. AccountCreated

### Description:

This event is emitted when a new Tokenbound Account is deployed.

### Parameters (type):

- `account_address (ContractAddress)`: The deployed contract address of the Tokenbound Account.
- `token_contract (ContractAddress)`: The contract address of the associated NFT.
- `token_id (u256)`: The unique ID of the associated NFT.

## Errors

`CALLER_IS_NOT_OWNER`

- This error is thrown when someone tries to create a Tokenbound Account without being the owner of the associated NFT.

## Additional Notes:

- Deterministic Addressing: The Registry contract calculates Tokenbound Account addresses deterministically, ensuring that the same NFT always results in the same account address.
- Security Considerations: Only the owner of an NFT can deploy its corresponding Tokenbound Account, ensuring that unauthorized users cannot create accounts.

## Glossary

- TBA (Tokenbound Account): An account linked to an NFT that can hold assets, interact with smart contracts, and act autonomously in the blockchain.
- [ERC-6551](https://github.com/horuslabsio/TBA/blob/v3/README.md#erc-6551-reference-implementation-on-starknet-snip-14): A standard that enables NFTs to have their own smart wallets, allowing them to own assets and interact with decentralized applications.
- Registry Contract: A smart contract responsible for deploying and managing Tokenbound Accounts and their addresses.
- Account Address: The unique deterministic address assigned to a Tokenbound Account, based on the NFT's contract address, token ID, and other parameters.


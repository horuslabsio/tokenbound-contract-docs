# Account Component Documentation

## Overview

The **AccountComponent** is designed for managing token-bound accounts in the StarkNet ecosystem. It allows for the interaction with non-fungible tokens (NFTs) and supports various operations related to account management, such as executing transactions and managing account states. This component is critical for enabling secure and efficient handling of account operations tied to specific NFTs.

## Purpose

The purpose of the **AccountComponent** is to provide a robust interface for developers to interact with token-bound accounts. It ensures that all operations related to account management are secure and follow the necessary protocol standards. The component allows for:

- Initialization of accounts tied to NFTs.
- Execution of transactions on behalf of the account owner.
- Retrieval of account state and ownership details.

## Available Functions

## External Functions

### 1. owner

```cairo
fn owner(self: @ComponentState<TContractState>) -> ContractAddress
```

- #### **Description**:

  Retrieves the owner of the associated NFT.

- #### **Parameters(type)**:

  `self(@ComponentState<TContractState>)`: The current state of the account component.

- #### **Returns**:
  ContractAddress - The address of the NFT owner.

### 2. token

```cairo
fn token(self: @ComponentState<TContractState>) -> (ContractAddress, u256, felt252)
```

- #### **Description**:
  Returns the contract address and token ID of the associated NFT.
- #### **Parameters(type)**:

  `self(@ComponentState<TContractState>)`: The current state of the account component.

- #### Returns:
  (ContractAddress, u256, felt252) - A tuple containing the token contract address, token ID, and the chain ID.

### 3. state

```cairo
fn state(self: @ComponentState<TContractState>) -> u256
```

- #### Description:
  Retrieves the current state of the account.
- #### **Parameters(type)**:

  `self(@ComponentState<TContractState>)`: The current state of the account component.

- #### Returns:
  u256 - The current account state.

### 4. supports_interface

```cairo
fn supports_interface(
    self: @ComponentState<TContractState>,
	interface_id: felt252) -> bool
```

- #### Description:
  Checks if the account supports a specified interface.
- #### Parameters(type):

  `self(@ComponentState<TContractState>)`: The current state of the account component.

  `interface_id(felt252)`: The interface ID to be checked against.

- #### Returns:
  bool - true if the interface is supported, otherwise false.

## Private Functions

### 1. initializer

```cairo
fn initializer(
    ref self: ComponentState<TContractState>,
    token_contract: ContractAddress,
    token_id: u256,
    registry: ContractAddress,
    implementation_hash: felt252,
    salt: felt252
)
```

- #### Description:

  This function initializes the account by setting the initial token contract and token ID. It verifies ownership, writes data to the account state, registers interfaces, and emits a `TBACreated` event

- #### Parameters(type):

  `token_contract(ContractAddress)`: The contract address of the NFT.

  `token_id(u256)`: The token ID of the NFT.

  `registry(ContractAddress)`: The registry contract address.

  `implementation_hash(felt252)`: The implementation hash.

  `salt(felt252)`: The salt used for account creation.

- #### Returns:
  None.

### 2. \_execute

```cairo
fn _execute(
    ref self: ComponentState<TContractState>, mut calls: Array<Call>
    ) -> Array<Span<felt252>>
```

- #### Description:

  This function executes a set of transactions specified in the `calls` parameter. It updates the account state, validates the transaction version, and emits a `TransactionExecuted` event after executing the calls.

- #### Parameters:

  `self: (ComponentState<TContractState>)`: A reference to the account's state.

  `calls`: An array of transactions to be executed.

- #### Returns:
  Array<Span<felt252>>: The data returned from the executed transactions.

### 3. \_update_state

```cairo
fn _update_state(ref self: ComponentState<TContractState>)
```

- #### Description:
  This function updates the account's state by hashing the previous state with the transaction nonce. This ensures that the state reflects the most recent transactions performed by the account.
- #### Parameter(type):
  `self: (ComponentState<TContractState>)`: A reference to the account's state.
- #### Returns:
  None

### 4. \_get_owner

```cairo
fn _get_owner(
    self: @ComponentState<TContractState>, token_contract: ContractAddress, token_id: u256
) -> ContractAddress
```

- #### Description:

  This internal function retrieves the owner of the NFT associated with the account by calling the `ownerOf` method on the token contract. It provides compatibility with both camelCase and snake_case methods for StarkNet contracts.

- #### Parameters:

  `self: (@ComponentState<TContractState>)`: The current state of the account component.

  `token_contract`: The contract address of the NFT.

  `token_id`: The token ID of the NFT.

- #### Returns:
  `ContractAddress`: The address of the NFT owner.

### 5. \_context

```cairo
_context(self: @ComponentState<TContractState>) -> (ContractAddress, felt252, felt252)
```

- #### Description:

  This internal function returns the deployment details of the account, including the registry address, implementation hash, and salt used during the account's creation.

- #### Parameters(types):

  `self (@ComponentState<TContractState>)`: The current state of the account component.

- #### Returns:

  - (ContractAddress, felt252, felt252):

    The contract address of the registry (ContractAddress).

    The implementation hash (felt252).

    The salt (felt252).

### 6. \_is_tokenbound_account

```cairo
fn _is_tokenbound_account(
	self: @ComponentState<TContractState>, account: ContractAddress, token_contract: ContractAddress, token_id: u256, registry: ContractAddress, implementation: felt252, salt: felt252) -> bool
```

- #### Description:

  This internal function checks if an account is a token-bound account (TBA). It verifies whether the provided account address matches the expected account based on the provided NFT and registry details.

- #### Parameters (type):

  `self(@ComponentState<TContractState>)`: The current state of the account component.

  `account(ContractAddress)`: The address of the account to check.

  `token_contract(ContractAddress)`: The contract address of the associated NFT.

  `token_id(u256)`: The token ID of the associated NFT.

  `registry(ContractAddress)`: The contract address of the registry.

  `implementation(felt252)`: The hash of the implementation contract.

  `salt(felt252)`: A unique value used during account creation.

- #### Returns:
  `bool`: True if the account is a token-bound account, False otherwise.

### 7. \_get_token

```cairo
fn _get_token(
	self: @ComponentState<TContractState>) -> (ContractAddress, u256, felt252)
```

- #### Description:

  This internal function retrieves the contract address and token ID of the NFT associated with the account, as well as the chain ID of the current StarkNet transaction.

- #### Parameters(type):
  self(@ComponentState<TContractState>): The current state of the account component.
- #### Returns:

  `(ContractAddress, u256, felt252)`

  - The contract address of the NFT (ContractAddress).
  - The token ID of the NFT (u256).
  - The current chain ID (felt252).

### 8. \_execute_calls

```cairo
_execute_calls(
	ref self: ComponentState<TContractState>, mut calls: Array<Call>) -> Array<Span<felt252>>
```

- #### Description:

  This internal function processes and executes the specified calls (transactions). If a call fails, it will trigger an error. The function returns the data from all successfully executed calls.

- #### Parameters:

  `self(ComponentState<TContractState>)`: A reference to the account's state.

  `calls(Array<Call>)`: An array of transactions to be executed.

- #### Returns:

  `Array<Span<felt252>>`: The data returned from the executed transactions.

## Events

`TBACreated`

- #### Description:

  Emitted when a token-bound account (TBA) is initialized. It records details of the account and the associated NFT.

- #### Parameters(type):

  `account_address(ContractAddress)`: The address of the initialized account.

  `parent_account(ContractAddress)`: The address of the parent account.

  `token_contract(ContractAddress)`: The contract address of the NFT.

  `token_id(u256)`: The token ID of the NFT.

`TransactionExecuted`

- #### Description:

  Emitted when a transaction is executed by the account.

- #### Parameters(type):

  `hash(felt252)`: The transaction hash.
  `account_address(ContractAddress)`: The address of the account that executed the transaction.
  `response(Span<Span<felt252>>)`: The data returned by the executed transaction.

## Errors

### UNAUTHORIZED

- #### Description:
  Thrown when an action is performed without proper authorization.
- #### Value:
  `Account: unauthorized`

### INV_SIG_LEN

- #### Description:
  Thrown when a transaction signature has an invalid length.
- #### Value:
  `Account: invalid sig length`

### INV_TX_VERSION

- #### Description:
  Thrown when a transaction version is not supported.
- #### Value:
  `Account: invalid tx version`

## References

- [Token Bound SDK Documentation](https://docs.tbaexplorer.com/starknet-tokenbound-sdk/introduction-to-tokenbound)
- [Account Component code](https://github.com/horuslabsio/TBA/blob/v3/src/components/account/account.cairo)
- [Sample Contract Documentation](https://contracts-v2-docs.lens.xyz/contracts/libraries/ProfileLib.sol/library.ProfileLib)

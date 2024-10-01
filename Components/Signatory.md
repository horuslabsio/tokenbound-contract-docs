# Component Signatory

<!-- logo -->
<p align="center">
  <img width='200' src="https://1330392220-files.gitbook.io/~/files/v0/b/gitbook-x-prod.appspot.com/o/spaces%2Ft2nl6Q0J1Gip9JKyMW79%2Fuploads%2FLKtcg1GIyT3yDkZm4bau%2Fstarknet.svg?alt=media&token=58e39f1a-aa1a-47c6-b850-24f40b4671f1">
</p>


## Relation with Tokenbound Account

The Signatory Component enhance the functionalities of the Tokenbound Account (TBA), and also the security that ERC-6551 provides. Tokenbound (TBA) is a suite of open-source tools that allow developers to easily integrate ERC-6551 accounts into their applications. This innovative approach empowers NFTs with their own StarkNet identity, enabling them to perform transactions, interact with decentralized applications.

## The Tokenbound suite includes:

### ERC-6551 Account Implementation:
A standardized implementation that ensures consistency and security across different NFT projects on StarkNet. <br/>
 https://github.com/Starknet-Africa-Edu/TBA/blob/main/src/account/account.cairo

### Front-end SDK: 
Tools for developers to easily interact with ERC-6551 accounts within their StarkNet applications, enhancing user experience and functionality. <br/>
https://github.com/Starknet-Africa-Edu/TBA-SDK 

### Account Explorer UI: 
An account explorer UI for viewing and interacting with ERC-6551 accounts <br/>
https://www.tbaexplorer.com/ 

## Relation to ERC-6551
ERC-6551 introduces a system where every NFT is assigned a wallet address, allowing NFTs to own assets, interact with decentralized applications, and act as independent agents. This component integrates with ERC-6551 by validating the ownership of the NFT and ensuring that any actions performed are authenticated by the wallet assigned to the NFT, or by a permissioned address.

## Components and Structure
The contract architecture is meticulously designed to leverage the ERC-6551 standard, facilitating the creation and management of Tokenbound Account. This structure ensures that each NFT is associated with a unique smart contract wallet, enhancing both functionality and security. The system comprises several key components and libraries, each aligned with the principles of ERC-6551.

### AccountComponent:
By leveraging the AccountComponent, the SignatoryComponent ensures that every NFT has a corresponding smart contract wallet, where ownership and asset control are strictly regulated. This integration is critical, as the AccountComponent keeps track of the NFT owner's identity and guarantees that only this owner or an authorized signer will be able to perform operations on the TBA. This link between the NFT, its account, and the SignatoryComponent ensures that TBA is secure, enabling NFTs to manage assets and participate in more complex interactions, such as decentralized finance (DeFi) activities or governance mechanisms.


### PermissionableComponent

The PermissionableComponent within the SignatoryComponent allows TBA to expand their flexibility by incorporating delegated permissions. TBA can enable multiple participants to interact with the NFT’s account by assigning permissions to different addresses. This is essential for scenarios where third parties, such as operators or developers, need access to the TBA's functions without compromising security. By managing these permissions dynamically, the SignatoryComponent extends the versatility of TBA.

### The ISRC6Dispatcher 
The ISRC6Dispatcher strengthens the security of TBA by standardizing how signatures are validated within the SignatoryComponent. Signatures are crucial in blockchain interactions, as they authenticate transactions and ensure they originate from authorized addresses. The ISRC6Dispatcher verifies that signatures conform to the ISRC6 standard, ensuring compatibility and security across different platforms and smart contracts. This compatibility assurance guarantees that signature validation processes are fully aligned with ERC 6551 compliant contracts, adding an additional layer of security by enforcing standardized signature verification and mitigating potential vulnerabilities.

## Functions 

### _base_signer_validation
Validates the NFT owner's wallet address, assigned under ERC-6551, ensuring it is a legitimate signer.

self: @ComponentState<TContractState>: This represents the state of the contract or component, likely referring to the state of the 
ERC-6551 account or another contract interacting with it.

signer: ContractAddress: This is the address of the wallet attempting to perform some operation that requires ownership validation.

Account.owner(): This method retrieves the wallet address that owns the account, which in this context is an NFT's associated Token Bound Account (TBA). 
```
fn _base_signer_validation(
            self: @ComponentState<TContractState>, signer: ContractAddress
        ) -> bool {
            let account = get_dep_component!(self, Account);
            let owner = account.owner();

            // validate
            if (signer == owner) {
                return true;
            } else {
                return false;
            }
        }
```

### _permissioned_signer_validation

The _permissioned_signer_validation function plays a crucial role in managing access to Tokenbound Account (TBA) by allowing designated entities, to interact with the account. 

Parameters:

self: @ComponentState<TContractState>: This parameter represents the current state of the component, allowing the function to access various component states and dependencies.

signer: ContractAddress: This is the address of the entity (either the NFT owner or a delegated address) that is attempting to sign or perform actions on the TBA.

```
 fn _permissioned_signer_validation(
            self: @ComponentState<TContractState>, signer: ContractAddress
        ) -> bool {
            let account = get_dep_component!(self, Account);
            let owner = account.owner();

            // check if signer has permissions
            let permission = get_dep_component!(self, Permissionable);
            let is_permissioned = permission._has_permission(owner, signer);

            // validate
            if (signer == owner) {
                return true;
            } else if (is_permissioned) {
                return true;
            } else {
                return false;
            }
        }
```

### _is_valid_signature

This function checks whether a given digital signature is valid. It first verifies the signature's length and then interacts with the ISRC6Dispatcher to determine whether the provided signature matches the expected value. If the validation is successful, it returns VALIDATED; otherwise, it throws an INVALID_SIGNATURE error.

```
 fn _is_valid_signature(
            self: @ComponentState<TContractState>, hash: felt252, signature: Span<felt252>
        ) -> felt252 {
            let account = get_dep_component!(self, Account);
            let owner = account.owner();

            // validate signature length
            let signature_length = signature.len();
            assert(signature_length == 2_u32, Errors::INV_SIG_LEN);

            // validate
            let owner_account = ISRC6Dispatcher { contract_address: owner };
            if (owner_account.is_valid_signature(hash, signature) == starknet::VALIDATED) {
                return starknet::VALIDATED;
            } else {
                return Errors::INVALID_SIGNATURE;
            }
        }
``` 

## Error Handling
##### INV_SIG_LEN: Triggered for invalid signature length.
##### UNAUTHORIZED: Triggered for unauthorized signers, ensuring only the NFT owner or permissioned entities can sign.
##### INVALID_SIGNATURE: Triggered when the signature does not match the expected value from the ERC-6551-compliant wallet.

## Files for Reference

Signatory Code <br/>
https://github.com/horuslabsio/TBA/blob/v3/src/components/signatory/signatory.cairo

Starknet Tokenbound SDK <br/>
https://docs.tbaexplorer.com/

Glossary <br/>
https://docs.tbaexplorer.com/starknet-tokenbound-sdk/glossary

SDK <br/>
https://docs.tbaexplorer.com/starknet-tokenbound-sdk/glossary


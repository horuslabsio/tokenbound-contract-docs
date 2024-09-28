## AccountV3
Account version 3 is a smart contract that creates Token Bound Accounts for NFTs. In addition to the features that existed on the version 2, it has two new features which are the lockable feature that allows to lock and unlock account and permissionable feature respectively.

### Functions

#### Constructor
The constructor function initializes certain parameters that will be executed once when the contract is deployed.

```
    fn constructor(
        ref self: ContractState,
        token_contract: ContractAddress,
        token_id: u256,
        registry: ContractAddress,
        implementation_hash: felt252,
        salt: felt252
    );
    
```
    
#####  Parameters
|    Name                |   Type              | Description  |
|------------------------|---------------------|--------------
|  `ref self`            |  `ContractState`    |An instance of the TBA contract.   | 
|  `token_contract`      |  `ContractAddress`  |The address of the NFT.            | 
|  `token_id`            |  `u256`             |The NFT token ID being transferred.| 
|  `registry`            |  `ContractAddress`  |The is responsible for calculating account address for each NFT. Registry contract address for sepolia: `0x23a6d289a1e5067d905e195056c322381a78a3bc9ab3b0480f542fad87cc580`. 
| 
|  `implementation_hash` |  `felt252`          | AccountV3 class hash. Account contract implementation hash for a TBA.| 
|  `salt`                |  `felt252`          | A unique ID that differentiates one TBA form another.| 

  ######              ACCOUNTV3 IMPLEMENTATION
  
####   on_erc721_received 
  This function is called to check the receipt of an NFT. It ensures that the ERC-721 token being transferred is not a token to which the account is already bound, in other to avoid account ownership cycle.
  
```
    fn on_erc721_received(
            self: @ContractState,
            operator: ContractAddress,
            from: ContractAddress,
            token_id: u256,
            data: Span<felt252>
        ) -> felt252;
```
        
#####  Parameters
|    Name                 |      Type            |  Description  |
|-------------------------|----------------------|--------------
| `self`                  |   `ContractState `   | An instance of the TBA contract             |
| `operator`              |   `ContractAddress`  | The address that sent the NFT.                   |
| `from`                  |   `ContractAddress`  | The address of the caller of`safe_transfer_from`.|
| `token_id`              |   `u256 `            | The NFT token ID being transferred.              |
| `data`                  |   `Span<felt252>`    | Additional data of the NFT.                      |
    

#### Context
Context gives deployment details of the TBA. It is used in cases of cross interaction.

```
    fn context(self: @ContractState) -> (ContractAddress, felt252, felt252)
```

 ######         SIGNATORY IMPLEMENTATION
 
#### is_valid_signer
This function checks if the signer's contract address passed is the NFT owner's address,or permissioned address.

```
    fn is_valid_signer(self: @ContractState, signer: ContractAddress) -> bool;
    
```  
 
#### is_valid_signature
It checks if the signature is valid and returns `starknet::valid` and returns `starknet::invalid` if the signature is invalid.

```
    `fn is_valid_signature(
            self: @ContractState, hash: felt252, signature: Span<felt252>
        ) -> felt252;`
```        


    
######    EXECUTABLE IMPLEMENTATION

#### Execute
This function executes transactions on the TokenBound  account. It must be called by `is_valid_signer` and the account must not be locked.

```
    fn execute(ref self: ContractState, mut calls: Array<Call>) -> Array<Span<felt252>>;
```
 
    
######   UPGRADEABLE IMPLEMENTATION

#### upgrade
 It upgrades the Token Bound Account by taking a `new_class_hash` and replacing it with the previous `class_hash`. An account can only be upgraded by the owner and the upgrade can not be done when the account is locked.
 
```
    fn upgrade(ref self: ContractState, new_class_hash: ClassHash);
```
    
######     LOCKABLE IMPLEMENTATION
    
#### lock
 Locks an account within a specified period of time which is measured in seconds. the function can only be called by`is_valid_signer`.
 
```
    fn lock(ref self: ContractState, lock_until: u64)
```
   
#### is_locked
  checks if an account is locked and returns true if it is locked and false if it is not. If the account is locked, it returns the time left until the account is unlocked.
  
```
    fn is_locked(self: @ContractState) -> (bool, u64);
```
    
######     PERMISSIONABLE IMPLEMENTATION
    
####  set_permission
 It sets permission for an account by specifying addresses that are allowed to carryout certain function calls for the owner.
  
```
    fn set_permission(
            ref self: ContractState,
            permissioned_addresses: Array<ContractAddress>,
            permissions: Array<bool>
        );
``` 
    
#####  Parameters
|    Name                 |      Type                 |  Description  |
|-------------------------|---------------------------|--------------
| `self`                  |  `ContractState`          | An instance of the TBA contract.                      |
| `permissioned_addresses`|  `Array<ContractAddress>` | Array of addresses the TBA owner is to permission. |
| `permissions`           |  `Array<bool>`            | Values of permissions for `permissioned_addresses`.   |

    
####  has_permission
 Checks if the `contractaddress` passed is a `permissioned_address` by the owner. It returns true if permission has been set  for the Address, else it returns false.
    
```
    fn has_permission(
            self: @ContractState, owner: ContractAddress, permissioned_address: ContractAddress
        ) -> bool; 
```
#####  Parameters
|    Name                 |      Type                 |  Description  |
|-------------------------|---------------------------|--------------
| `self`                  |  `ContractState`          | An instance of the TBA contract.                                |
| `owner`                 |  `ContractAddress`        | Address of tokenbound account owner that allows the permission. |
| `permissioned_address`  |  `ContractAddress`        | Address to check permission for.                                |



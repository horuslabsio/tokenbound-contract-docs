# UpgradeableComponent Documentation

## Summary

The `UpgradeableComponent` is a module designed for StarkNet contracts that require upgradability. It allows a contract to replace its current class hash with a new one, effectively updating its implementation while maintaining the same contract address and state. This component is particularly useful for token-bound accounts or contracts that need to evolve over time without disrupting their on-chain identity or data.

---

## Module Structure

```rust
#[starknet::component]
pub mod UpgradeableComponent {
    // Imports
    // Storage
    // Events
    // Errors
    // Private Functions
}
```

The `UpgradeableComponent` module is structured as follows:

- **Imports**: Necessary modules and components are imported for functionality.
- **Storage**: Defines the storage structure for the component.
- **Events**: Defines events emitted by the component.
- **Errors**: Defines error messages used in the component.
- **Private Functions**: Implements the core functionality of the component.

---

## Imports

```rust
use starknet::{
    ClassHash, SyscallResultTrait, get_contract_address, ContractAddress
};
use core::num::traits::zero::Zero;

use token_bound_accounts::components::account::account::AccountComponent;
use openzeppelin::introspection::src5::SRC5Component;
```

- **StarkNet Modules**: Provide access to StarkNet-specific types and syscalls.
  - `ClassHash`, `ContractAddress`, `get_contract_address`, `SyscallResultTrait`.
- **Core Modules**: Standard Rust library traits.
  - `Zero` trait to check if numbers are zero.
- **Token Bound Accounts**: Integrates with `AccountComponent` for account management.
- **OpenZeppelin Components**: Integrates with `SRC5Component` for standard compliance.

---

## Data Structures

### Storage

```rust
#[storage]
pub struct Storage {}
```

- **Description**: The `Storage` struct is currently empty but serves as a placeholder for potential future storage needs within the `UpgradeableComponent`.

---

## Events

### TBAUpgraded

**Event Signature**:

```rust
#[event]
#[derive(Drop, starknet::Event)]
pub enum Event {
    TBAUpgraded: TBAUpgraded
}

#[derive(Drop, starknet::Event)]
pub struct TBAUpgraded {
    pub account_address: ContractAddress,
    pub class_hash: ClassHash
}
```

**Description**:

The `TBAUpgraded` event is emitted when the contract successfully upgrades to a new class hash. It provides transparency and allows external observers to track contract upgrades.

**Parameters**:

- `account_address: ContractAddress`  
  The address of the contract that was upgraded.

- `class_hash: ClassHash`  
  The new class hash to which the contract has been upgraded.

---

## Errors

### INVALID_CLASS

```rust
pub mod Errors {
    pub const INVALID_CLASS: felt252 = 'Class hash cannot be zero';
}
```

**Description**:

The `INVALID_CLASS` error is thrown when an attempt is made to upgrade the contract using a zero class hash. This validation step ensures that the contract cannot be upgraded to an invalid or non-existent implementation.

---

## Functions

### `_upgrade`

**Function Signature**:

```rust
fn _upgrade(
    ref self: ComponentState<TContractState>, 
    new_class_hash: ClassHash
)
```

**Description**:

The `_upgrade` function replaces the current contract's class hash with a new one provided by `new_class_hash`. It performs necessary state updates, validates the input, executes the class replacement, and emits an event to log the upgrade.

**Parameters**:

- `new_class_hash: ClassHash`  
  The class hash of the new contract implementation to upgrade to.

**Return Values**:

- **None**

**Functionality Steps**:

1. **State Update**: Updates the internal state of the `AccountComponent`.
2. **Validation**: Checks if `new_class_hash` is zero and throws `INVALID_CLASS` error if so.
3. **Upgrade Execution**: Calls `replace_class_syscall` to replace the contract's class hash.
4. **Event Emission**: Emits the `TBAUpgraded` event with the contract's address and the new class hash.

**Implementation**:

```rust
fn _upgrade(
    ref self: ComponentState<TContractState>, 
    new_class_hash: ClassHash
) {
    // 1. Update state
    let mut account_comp_mut = get_dep_component_mut!(ref self, Account);
    account_comp_mut._update_state();

    // 2. Validate new class hash is not zero
    assert(!new_class_hash.is_zero(), Errors::INVALID_CLASS);

    // 3. Upgrade account
    starknet::syscalls::replace_class_syscall(new_class_hash).unwrap_syscall();

    // 4. Emit upgrade event
    self.emit(
        TBAUpgraded {
            account_address: get_contract_address(),
            class_hash: new_class_hash
        }
    );
}
```

**Possible Errors**:

- `INVALID_CLASS`  
  Thrown when `new_class_hash` is zero.

**Usage Example**:

```rust
// Assuming `upgradeable_component` is an instance of UpgradeableComponent
let new_class_hash = ClassHash::from_hex("0x0123456789abcdef").unwrap();
upgradeable_component._upgrade(new_class_hash);
```

**Notes**:

- The `_upgrade` function is a **private** function intended for internal use within the component.
- Access control must be implemented to restrict who can perform the upgrade.
- The function ensures state consistency and transparency through event emission.

---

## Private Functions

The `UpgradeableComponent` defines its functions within an implementation block marked with `#[generate_trait]`, which generates a trait for the component.

```rust
#[generate_trait]
pub impl UpgradeablePrivateImpl<
    TContractState,
    +HasComponent<TContractState>,
    +Drop<TContractState>,
    impl Account: AccountComponent::HasComponent<TContractState>,
    impl SRC5: SRC5Component::HasComponent<TContractState>
> of UpgradeablePrivateTrait<TContractState> {
    // Private function implementations
}
```

**Explanation**:

- The implementation block defines functions that are private to the component.
- It uses generics and trait bounds to ensure the necessary components are available.
- Implements the `UpgradeablePrivateTrait` for the contract state.

---

## Integration

### AccountComponent

- **Purpose**: Manages account-related state and functionality.
- **Integration**: The `_upgrade` function calls `_update_state` on the `AccountComponent` to ensure the account's state is up to date before proceeding with the upgrade.

### SRC5Component

- **Purpose**: Ensures compliance with OpenZeppelin's SRC5 standard.
- **Integration**: Declares dependencies to maintain standard compliance, which may be required for certain functionalities or interfaces.

---

## Example Usage in a Contract

Below is an example of how to integrate the `UpgradeableComponent` into a contract with access control.

```rust
#[starknet::contract]
mod MyUpgradableContract {
    use super::UpgradeableComponent;
    use openzeppelin::access::ownable::OwnableComponent;

    #[storage]
    struct Storage {
        owner: ContractAddress,
    }

    #[constructor]
    fn constructor(ref self: ContractState<Self>) {
        OwnableComponent::constructor(ref self, get_caller_address());
    }

    #[external]
    fn upgrade(ref self: ContractState<Self>, new_class_hash: ClassHash) {
        // Ensure only the owner can upgrade
        OwnableComponent::only_owner(ref self);

        // Perform the upgrade
        UpgradeableComponent::_upgrade(ref self, new_class_hash);
    }
}
```

**Explanation**:

- **Access Control**: Uses `OwnableComponent` to restrict the `upgrade` function to the contract owner.
- **Upgrade Function**: Exposes a public `upgrade` function that internally calls `_upgrade` from `UpgradeableComponent`.

---


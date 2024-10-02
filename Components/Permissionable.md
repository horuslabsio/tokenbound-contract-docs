# Permissionable Component Documentation
The **Permissionable** component is primarily used to:
- Set permissions for specific actions to authorized addresses (executors).
- Verify whether a particular address has permission to perform certain operations.

By leveraging this component, developers can build applications where users maintain control over key functions while safely delegating specific tasks to other addresses.
## Functions

### 1. setPermission
``` 
fn _set_permission(
    ref self: ComponentState<TContractState>,
    permissioned_addresses: Array<ContractAddress>,
    permissions: Array<bool>
)
 ```

- **Purpose**: Assigns or revokes a specific permission to an address for a particular profile.
  
#### Parameters

| **Name**            | **Type**                 | **Description**                                                                                                                                                   |
|--------------------------|--------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `ref sef`  | `ComponentState` | An instance of the component.
| `permissioned_addresses`  | `Array<ContractAddress>` | An array of addresses that need permission updates. The length of this array must match the length of the `permissions`. array.                                      |
| `permissions`             | `Array<bool>`            | A corresponding array of boolean values where `true` grants permission and `false` revokes it.                                                                    |


### 2. hasPermission
```
fn _has_permission(
    self: @ComponentState<TContractState>,
    owner: ContractAddress,
    permissioned_address: ContractAddress
) -> bool

```

- **Purpose**: This function checks whether a specific `permissioned_address` has been granted permission by the given account owner.



#### Parameters

This function checks whether a specific `permissioned_address` has been granted permission by the given account owner.

| **Name**           | **Type**                 | **Description**                                                                                                                                |
|-------------------------|--------------------------|------------------------------------------------------------------------------------------------------------------------------------------------|
| `owner`                 | `ContractAddress`        | The owner of the token-bound account who granted or revoked the permission.                                                                    |
| `permissioned_address`  | `ContractAddress`        | The address for which the permission status needs to be checked.                                                                               |

**Returns**: A `bool` value:
- `true` if the `permissioned_address` has been granted permission by the `owner`.
- `false` if the `permissioned_address` has no permission.




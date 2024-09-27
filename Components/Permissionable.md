# Permissionable Component Documentation

## Purpose
The **Permissionable** component is primarily used to:
- Set permissions for specific actions to authorized addresses (executors).
- Verify whether a particular address has permission to perform certain operations.

By leveraging this component, developers can build applications where users maintain control over key functions while safely delegating specific tasks to other addresses.


## Functions

### 1. setPermission
``` 
setPermission(uint256 profileId, address executor, bytes32 permission, bool isGranted)
 ```

- **Purpose**: Assigns or revokes a specific permission to an address for a particular profile.
- **Type**: External function.
  
#### Parameters

| Name          | Type      | Description                                                                         |
|---------------|-----------|-------------------------------------------------------------------------------------|
| `profileId`   | `uint256` | The profile ID for which the permission is being set.                                |
| `executor`    | `address` | The address of the executor to whom the permission is being granted or revoked.      |
| `permission`  | `bytes32` | The specific permission being granted or revoked (e.g., "admin", "editProfile").     |
| `isGranted`   | `bool`    | Boolean value determining whether the permission is granted (`true`) or revoked (`false`). |

#### Example Usage:
```
setPermission(1, 0xAbc123..., keccak256("editProfile"), true);
```
In this example, the executor `0xAbc123...` is granted permission to "editProfile" for the profile with ID `1`.


### 2. `hasPermission
```
hasPermission(uint256 profileId, address executor, bytes32 permission) external view returns (bool)
```

- **Purpose**: Checks whether a specific address has been granted permission to perform a certain action for a given profile.
- **Type**: External view function.

#### Parameters

| Name          | Type      | Description                                                                         |
|---------------|-----------|-------------------------------------------------------------------------------------|
| `profileId`   | `uint256` | The profile ID to check permissions for.                                             |
| `executor`    | `address` | The address of the executor whose permissions are being checked.                     |
| `permission`  | `bytes32` | The specific permission being verified (e.g., "admin", "editProfile").               |

#### Returns

| Type   | Description                                             |
|--------|---------------------------------------------------------|
| `bool` | Returns `true` if the address has the permission, `false` otherwise. |

#### Example Usage:
```
bool canEdit = hasPermission(1, 0xAbc123..., keccak256("editProfile"));
```
In this example, the function checks whether the address `0xAbc123...` has the "editProfile" permission for the profile with ID `1`. If the address has the permission, the function returns `true`.


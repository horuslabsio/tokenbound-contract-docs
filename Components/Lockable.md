##  Lockable component documentation

### Lockabke component
The Lockable component enables the temporary locking of accounts, restricting access to specific operations for a set period. This feature is useful when an account needs to be secured or its access limited for a defined amount of time.

#### Purpose

The Lockable component is designed to provide functionality for temporarily restricting actions on a token-bound account. It introduces a mechanism to "lock" an account for a defined period, during which only limited operations (if any) can be performed. This is particularly useful for scenarios where access control must be strictly enforced based on time conditions, such as:

* It ensures that certain critical operations cannot be executed while the account is in a locked state.

* It Implementing cooldown periods where an account becomes temporarily inaccessible, preventing unauthorized or unintended interactions.

This component enhances the security of token-bound accounts by allowing developers to lock an account based on specific conditions and unlock it automatically when the time has passed.

### Functions

### The `_lock ` function

`fn _lock(ref self: ComponentState<TContractState>, lock_until: u64)`

* **Purpose**: Locks the token-bound account until the specified timestamp (lock_until). This function is typically invoked when the owner or authorized party wishes to temporarily restrict access to certain account functions. For example, the account could be locked to prevent transfers or other critical operations during a sensitive period.
 
* Type: Private function.

#### **Parameters**


| Name         | Type  | Description                                                                                                                                                                                            |
 Name | Type | Description |
| -------- | -------- | -------- |
| `ref self`   | `ComponentState<TContractState>`  |The component state of the contract implementing the lockable functionality. This holds the current state of the lock mechanism.      |
| `lock_until`   | `u64` | A timestamp (in UNIX format) specifying until when the contract is locked. No further actions can be taken until this time has passed. |

Detailed Behavior:
1.  Lock Time  Validation: Before locking the account, the function checks whether the `lock_until` timestamp is within the allowed maximum lock period. The maximum duration is defined as one year `(YEAR_TO_SECONDS = 31,536,000 seconds)`. If the provided lock time exceeds this limit, the function will throw an `EXCEEDS_MAX_LOCK_TIME error`.

2.  Lock Status Check: The function also verifies if the account is already locked by calling the internal `_is_locked` function. If the account is currently locked, it will raise an `LOCKED_ACCOUNT` error to prevent overwriting the lock.

3.  State Update: If the above checks pass, the account state is updated to reflect the new lock duration. The `lock_until` value is written to the contract’s storage, ensuring that any subsequent checks for lock status reference this updated value.

4.  Event Emission: Once the account is successfully locked, the contract emits an `AccountLocked` event. This event contains the address of the account being locked, the timestamp when the lock was initiated, and the time until which the lock will remain in place.


### 2. **_is_locked**

`fn _is_locked(self: @ComponentState<TContractState>) -> (bool, u64)`

* **Purpose**: Checks the current lock status of the account. It determines whether the account is still within its locked period and, if so, calculates the remaining time until it will be unlocked. This function plays a key role in other contract logic, where actions might be conditionally allowed or restricted based on the account’s lock state.

**Parameters**
|     Name | Type     | Description |
| -------- | -------- | -------- |
| `self`   | `@ComponentState<TContractState>`  |A reference to the component state of the contract. This is used to check if the lock mechanism is currently active.      |


**Returns**
| Type     | Description |
| -------- | -------- |
| `(bool, u64)`  | A timestamp (in UNIX format) specifying until when the contract is locked. No further actions can be taken until this time has passed. |



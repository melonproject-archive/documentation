# Vault

### General

### Vault.sol

##### Description

The Vault makes use of the `auth` modifier from the DSAuth dependency. The `auth` functionality ensures the precise provisioning of call permissions, and focuses on granting `owner` or the current contract call permissions.

The contract provides extra security around ERC20 deposit and withdraw functionality, into- and from the vault, respectively.  This is achieved by ensuring that only the owner can withdraw from the vault when the vault's `locked` state is `false`.

The Vault contract is created from the vaultFactory contract, which creates a new instance of the `Vault` given the `hub` address, register the address of the newly created vault contract as a child of the vaultFactory and finally emits an event, broadcasting the creation event along with the address of the vault contract.

&nbsp;

##### Inherits from
Spoke (link)

&nbsp;

##### On construction

Requires the `Hub` address and uses this to instantiate itself as a `Spoke`.

&nbsp;

##### Structures
None

&nbsp;

##### Modifiers

`onlyUnlocked()`

Before any execution, this modifier requires that the vault contract's `locked` state variable is `false`.

&nbsp;

##### Public State Variables

`bool public locked`

A public boolean state variable which indicates the lock state of the vault.

&nbsp;

##### Public Functions

&nbsp;

`function lockdown() auth`

This function requires that the caller is the `owner` or the current contract. The function only sets the `locked` state variable to `true`.

&nbsp;

`function unlock() auth`

This function requires that the caller is the `owner` or the current contract. The function only sets the `locked` state variable to `false`.

&nbsp;

`function deposit(address token, uint amount) auth`

This function requires that the caller is the `owner` or the current contract. This function calls the `transferFrom()` ERC20 function of the provided asset token contract address, transferring ownership of the provided amount to the custody of the vault contract.

&nbsp;

`function withdraw(address token, uint amount) onlyUnlocked auth`

This function requires that the caller is the `owner` or the current contract, and that the `locked` state of the vault be `false`. This function calls the `transfer()` ERC20 function of the provided asset token contract address, transferring ownership of the provided amount from the vault to the custody of the `owner`.

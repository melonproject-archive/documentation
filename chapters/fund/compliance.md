# Compliance

## General

The contracts below define the business logic for the screening of investor addresses allowed to- or prevented from subscribing to a Melon fund.

The current implementation does not affect an address status after the fact. Blacklisting does not affect an invested address's current invested status or ability to redeem, but will prevent future subscriptions. The whitelist can be seen as a positive filter, creating a known universe of allowed investor addresses. Investor addresses can be added to- or removed from either list, individually or in batch by the fund manager (`owner`).

## Whitelist.sol

#### Inherits from
Policy, DSAuth (link)

#### Description

This contract defines a positive filter list, against which subscribing addresses are verified for membership. Member addresses are allowed for subscription.

#### Inherits from
Policy, DSAuth (links)

#### On Construction
The Whitelist contract requires an array of addresses which are added to the `whitelisted` mapping.


#### Structs
None.

#### Enums
None.

#### Modifiers
None.

#### Public State Variables

`mapping (address => bool) whitelisted`

Mapping which designates an investor address as being eligible to subscribe to the fund.


#### Public Functions

`function Whitelist(address[] _preApproved) public`

Constructor function which enables the bulk addition of many addresses designated as eligible for subscription to the fund.


`function addToWhitelist(address _who) public auth`

This function requires that the caller is the `owner` or the current contract. This function sets the `whitelisted` mapping for the provided address to `true`.


`function removeFromWhitelist(address _who) public auth`

This function requires that the caller is the `owner` or the current contract. This function sets the `whitelisted` mapping for the provided address to `false`. Addresses which had previously subscribed and are invested can not subsequently subscribe further amounts.

`function batchAddToWhitelist(address[] _members) public auth`

This function requires that the caller is the `owner` or the current contract. The function, with one transaction, enables multiple addresses in the `whitelisted` mapping to be set to `true`.

`function batchRemoveFromWhitelist(address[] _members) public auth`

This function requires that the caller is the `owner` or the current contract. The function, with one transaction, enables multiple addresses in the `whitelisted` mapping to be set to `false`. Addresses which had previously subscribed and are invested can not subsequently subscribe further amounts.


`function rule(bytes4 sig, address[5] addresses, uint[3] values, bytes32 identifier) external view returns (bool)`

This function is called by the Policy Manager when functions registered for this specific policy are called. See further documentation under Policy Manager (link).


`function position() external view returns (uint)`

This function is called by the Policy Manager to determine whether the policy should be executed and evaluated as a pre-condition (0) or as a post-condition (1). Compliance contracts are called as a pre-condition to subscription and therefore return 0 during the whitelist compliance check. See further documentation under Policy Manager (link).





## Blacklist.sol

#### Description

This contract defines a negative filter list, against which subscribing addresses are verified for membership. Member addresses are disallowed for subscription.

#### Inherits from
Policy, DSAuth (links)

#### On Construction
The Blacklist contract requires an array of addresses which are added to the `blacklisted` mapping.

#### Structs
None.

#### Enums
None.

#### Modifiers
None.

#### Public State Variables

`mapping (address => bool) blacklisted`

Mapping which designates an investor address as being ineligible to subscribe to the fund.


#### Public functions

`function Blacklist(address[] _preBlacklisted) public`

Constructor function which enables the bulk addition of many addresses designated as ineligible for subscription to the fund.


`function addToBlacklist(address _who) public auth`

This function requires that the caller is the `owner` or the current contract. This function sets `blacklisted` mapping for the provided address to `true`.


`function removeFromBlacklist(address _who) public auth`

This function requires that the caller is the `owner` or the current contract. This function sets the `blacklisted` mapping for the provided address to `false`. Addresses which previously were denied subscription would now be permitted.


`function batchAddToBlacklist(address[] _members) public auth`

This function requires that the caller is the `owner` or the current contract. The function, with one transaction, enables multiple addresses in the `blacklisted` mapping to be set to `true`.


`function batchRemoveFromWhitelist(address[] _members) public auth`

This function requires that the caller is the `owner` or the current contract. The function, with one transaction, enables multiple addresses in the `blacklisted` mapping to be set to `false`. Addresses which previously were denied subscription would now be permitted.


`function rule(bytes4 sig, address[5] addresses, uint[3] values, bytes32 identifier) external view returns (bool)`

This function is called by the Policy Manager when functions registered for this specific policy are called. See further documentation under Policy Manager (link).


`function position() external view returns (uint)`

This function is called by the Policy Manager to determine whether the policy should be executed and evaluated as a pre-condition (0) or as a post-condition (1). Compliance contracts are called as a pre-condition to subscription and therefore return 0 during the blacklist compliance check. See further documentation under Policy Manager (link).

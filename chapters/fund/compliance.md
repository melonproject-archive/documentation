# Compliance

## Whitelist.sol

Inherits policy, DSAuth (link)

Description
...

Inherits from Policy, DSAuth (links)

On Construction
Sets the hub of the spoke.


Structs
None.

Enums
None.


Public State Variables


`mapping (address => bool) whitelisted`

Mapping which designates an investor address as being eligible to subscribe to the fund.


Public Functions

`function Whitelist(address[] _preApproved) public`

Function which enables the bulk addition of many addresses designated as eligible to subscribe to the fund.


`function addToWhitelist(address _who) public auth`

Function which enables a single address to be designated as eligible to subscribe to the fund.


`function removeFromWhitelist(address _who) public auth`

Function which enables a single address to be designated as ineligible to subscribe to the fund. Addresses which had previously subscribed and are invested can not subsequently subscribe further amounts.


`function batchRemoveFromWhitelist(address[] _members) public auth`

Function which enables a many addresses to be designated as ineligible to subscribe to the fund. Addresses which had previously subscribed and are invested can not subsequently subscribe further amounts.


`function rule(bytes4 sig, address[5] addresses, uint[3] values, bytes32 identifier) external view returns (bool)`

Document or Not?  Function called by the Policy Manager when functions registered for this specific policy are called. See further documentation under Policy Manager (link).


`function position() external view returns (uint)`

Document or Not?  Function called by the Policy Manager to determine whether the policy should be executed and evaluated as a pre-condition (0) or as a post-condition (1). See further documentation under Policy Manager (link).


## Blacklist.sol

Description
...

Inherits from Policy, DSAuth (links)

On Construction
Sets the hub of the spoke.

Structs
None.

Enums
None.


Public State Variables

`mapping (address => bool) blacklisted`

Mapping which designates an investor address as being ineligible to subscribe to the fund.


Public functions

`function Blacklist(address[] _preBlacklisted) public`

Function which enables the bulk addition of many addresses designated as ineligible to subscribe to the fund.


`function addToBlacklist(address _who) public auth`

Function which enables a single address to be designated as ineligible to subscribe to the fund.


`function removeFromBlacklist(address _who) public auth`

Function which enables a single address to be designated as eligible to subscribe to the fund.


`function batchAddToBlacklist(address[] _members) public auth`

Function which, with one transaction, enables amany addresses to be designated as ineligible to subscribe to the fund.


`function batchRemoveFromWhitelist(address[] _members) public auth`

Function which enables a many addresses to be designated as ineligible to subscribe to the fund. Addresses which had previously subscribed and are invested can not subsequently subscribe further amounts.


`function rule(bytes4 sig, address[5] addresses, uint[3] values, bytes32 identifier) external view returns (bool)`

Document or Not?  Function called by the Policy Manager when functions registered for this specific policy are called. See further documentation under Policy Manager (link).


`function position() external view returns (uint)`

Document or Not?  Function called by the Policy Manager to determine whether the policy should be executed and evaluated as a pre-condition (0) or as a post-condition (1). See further documentation under Policy Manager (link).

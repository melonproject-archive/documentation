# Compliance

## General

The contracts below define the business logic for the screening of investor addresses allowed to- or prevented from subscribing to a Melon fund.

In the Melon Protocol, the term "Compliance" revolves around the specifics of investing as an Investor _into_ Melon Fund. This is often referred to as a "Subscription". Details around which addresses, amounts and when Investors may subscribe to the Melon Fund are configured in-, and managed by the Compliance module. To clarify, in the traditional investment management industry, "compliance" is also used to refer to trading of underlying fund assets, as in a whether a certain asset or trade is compliant with portfolio guidelines, laws and regulations. This type of intra-portfolio asset level compliance is handled by the Risk Engineering module in the Melon Protocol.

The main use case for the Melon Fund Compliance module is the ability to create and maintain a whitelist for specific addresses. That is, the Investment Manager can explicitly define specific addresses which will then have the ability to subscribe to the Melon Fund.

Note that any listing of an address on the Compliance module whitelist only impacts the Investor's _ability_ to subscribe to the Melon Fund. An existing investment from a specific address will remain invested irrespective of any change in that address's whitelist status. The current implementation does not affect an address status after the fact. Blacklisting does not affect an invested address's current invested status or ability to redeem, but will prevent future subscriptions. The whitelist can be seen as a positive filter, creating a known universe of allowed investor addresses. Investor addresses can be added to- or removed from either list, individually or in batch by the fund manager (`owner`).

## Future Directions

The Melon Fund Compliance module potentially has great significance for regulators and regulatory frameworks. Possible use cases are: Regulators or KYC/AML providers may create and maintain their own whitelist of Investor addresses in a Melon Compliance module that individual Melon Funds could query; the ability for the Investment Manager to "Hard Close" or "Soft Close" subscriptions; the ability to cap subscription amounts.

Hard Close - A fund refuses all new investment subscriptions, usually due to capacity limitations for a specific strategy.

Soft Close - A fund refuses investment subscriptions new Investors, but accepts investment top-ups from existing Investors (addresses).

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

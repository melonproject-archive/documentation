

# Policy Manager

## General

The policyManager contract is the core of risk management and compliance policies.  Policies are individual contracts that define and enforce specific business logic codified within. Policies are registered with the Melon fund's policyManager contract for specific function call pertaining to trading fund positions or investor subscriptions.

The policyManager is embedded into specific function calls in other Spokes of the Melon fund as required through the modifiers described below.
&nbsp;

## policyManager.sol

#### Description

In many of the functions below an array of address `addresses`, an array of uint `values` and an `identifier` are passed as parameters. For the arrays, the order of the address or value in the array is semantically significant. The individual array positions, [n], are defined within the policyManager contract as follows:

`address[5] addresses`:

[0] order maker - the address initiating or offering the trade
[1] order taker - the address filling or partially filling the offered trade
[2] maker asset - the token address of the asset token intended by the order maker to exchange for another token asset, i.e. the taker asset
[3] taker asset - the token address of the asset token to be received by the offer maker in exchange for the maker asset
[4] exchange address - address of the exchange contract on which the order is to be placed

`uint[3] values`:

[0] maker token quantity - the maximum quantity of the maker asset token to be given by the order maker in exchange for the specified taker asset token
[1] taker token quantity - the maximum quantity of the taker asset token to be received by the order maker in exchange for the specified maker asset token
[2] Fill amount - the quantity of the taker token exchanged in the transaction. Must be less than or equal to the taker token quantity [1]

Finally, `identifier` and `sig` parameters are described below:

`bytes32 identifier` - order id for exchanges utilizing a unique, exchange-specific order identifier

`bytes4 sig` - the keccak256 hash of the plain text function signature of the function which triggers the specific policy validation.

&nbsp;

#### Inherits from

Spoke (link)

&nbsp;

#### On Construction

The PolicyManager contract is passed the corresponding Hub address and sets this as the hub state variable inherited from Spoke.

The PolicyManager contract is created from the PolicyManagerFactory contract, which creates a new instance of `PolicyManager` given the `hub` address, registering the address of the newly created PolicyManager contract as a child of the PolicyManagerFactory.

&nbsp;

#### Structs

`Entry`

 Member variables:  

 `Policy[] pre` - An array of Policy contract addresses which are registered to be validated as pre-conditions to defined function calls.

 `Policy[] post` - An array of Policy contract addresses which are registered to be validated as post-conditions to defined function calls.

&nbsp;

#### Enums

None.

&nbsp;

#### Modifiers

`modifier isValidPolicyBySig(bytes4 sig, address[5] addresses, uint[3] values, bytes32 identifier)`

This modifier ensures that `preValidate()` is called prior to applied function code and that `postValidate()` is called after the applied function code, sending the function signature hash `sig` as provided.

&nbsp;

`modifier isValidPolicy(address[5] addresses, uint[3] values, bytes32 identifier)`

This modifier ensures that `preValidate()` is called prior to applied function code and that `postValidate()` is called after the applied function code, sending the calling function signature hash `msg.sig`.

&nbsp;

#### Events

None.

&nbsp;

#### Public State Variables

`mapping(bytes4 => Entry) policies`

A mapping of bytes4 to an `Entry` struct.

&nbsp;

#### Public Functions

`function registerBatch(bytes4[] sig, address[] ofPolicies) public`

This function requires equal length of both array parameters. The function then iterates over the `sig` array calling the register() function, providing each function signature hash and the corresponding Policy contract address.  
&nbsp;

`function register(bytes4 sig, address ofPolicy) public`   

This function first ascertains whether the Policy being registered with the PolicyManager is to be executed as a pre- or post condition and then pushes the Policy with the corresponding signature hash on to the respective pre or post Policy array within the policies mapping. Once a Policy is registered, the condition defined within the Policy will be the standard against which the policy-registered function's consequential state changes will be tested. If the state changes pass the policy test, the function will continue execution unhindered and the state changes, e.g. a trade and the respective changes to token allocation) will become final as part of a transaction in a mined block. If the state changes do not pass the policy test, the transaction will revert and no state change will be affected.
&nbsp;

`function getPoliciesBySig(bytes4 sig) public view returns (address[], address[])`

This view function returns two address arrays (pre and post, respectively) containing all Policy contract addresses registered for the provided function signature hash. This gives the ability to query registered policies for a specific function call.
&nbsp;

`function preValidate(bytes4 sig, address[5] addresses, uint[3] values, bytes32 identifier) view public`

This view function calls the `validate()` function, explicitly passing the `pre` array from the `policies` mapping state variable filtered for Policies registered for the provided function signature hash `sig`.
&nbsp;

`function postValidate(bytes4 sig, address[5] addresses, uint[3] values, bytes32 identifier) view public`

This view function calls the `validate()` function, explicitly passing the `post` array from the `policies` mapping state variable filtered for Policies registered for the provided function signature hash `sig`.
&nbsp;

`function validate(Policy[] storage aux, bytes4 sig, address[5] addresses, uint[3] values, bytes32 identifier) view internal`

This internal view function receives a function-specific filtered array of Policy contract addresses and iterates over the array, calling each Policy's implemented `rule()` function. If the call to a Policy's `rule()` evaluates to `true`, execution and any state transition proceeds. If the call to a Policy's `rule()` evaluates to `false`, all execution and preliminary state transitions are reverted.  



# Policy Manager

## General

The policyManager contract is the core of risk management and compliance policies.  Policies are individual contracts that define and enforce specific business logic codified within. Policies are registered with the Melon fund's policyManager contract for specific function call pertaining to trading fund positions or investor subscriptions.

The policyManager is embedded into specific function calls in other Spokes of the Melon fund as required through the modifiers described below.
&nbsp;

## PolicyManager.sol

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

# Policies

## General

Polices are individual smart contracts which define rule or set of rules to be compared to the state of the Melon fund. Policies simply assess the current state of the Melon fund and resolve to a boolean decision, whether the action may be executed or not, returning `true` for allowed actions and `false` for disallowed actions. The Melon fund-specific Policies are deployed with parameterized values which the defined Policy logic uses to assess the permissibility of the action.

#### Pre- and Post-Conditionality
Policies are intended to be rules; they are intended to permit or prevent specific behavior or action depending on the *state* as compared to specified criteria.

Some Policies, due to the nature of the data required, can be immediately resolved based on the *current* state. The resolution of the Policy result is trivial because all data needed is at hand and must nod be derived or calculated. Such Policies can be defined as "pre-condition" Policies.

Other Policies may need to assess the consequence of the behavior or action before the logic can assess its permissibility relative to the defined rule. To do this, the result of the action must be derived or calculated, essentially asking, "What *will* the state be if this action is executed?" Such Policies can be defined as "post-condition" Policies.

On the blockchain and in smart contracts, we can use a fortunate side-effect of the process of mining and block finalization to help determine the validity of post-condition Policies. With post-condition Policies, the action or behavior is executed with the smart contract logic and the changed (but not yet finalized or mined) state is assessed against the logic and defined parameters of the post-condition Policy. In the case where this new state complies with the logic and criteria of the Policy, the action is allowed, meaning the smart contract execution is allowed to run to completion, the block is eventually mined and this new compliant state is finalized in that mined block. In the case where the new state does not comply with the logic and criteria of the Policy, the action is disallowed and the revert() function is called, stopping execution and discarding (or rolling back) all state changes. In calling the revert() function, gas is consumed to arrive at the reference state, but any unused gas is returned to the caller as the reference state is discarded.
&nbsp;

## Policy.sol

#### Description

The Policy contract is inherited by implemented Policies. This contract will be changed to an interface when upgraded to Solidity 0.5, as enums and structs are allowed to be defined in interfaces in that version.
&nbsp;

#### Inherits from

None.

&nbsp;

#### On Construction

None.

&nbsp;

#### Structs

None.

&nbsp;

#### Enums

`Applied` - An enum which characterizes the conditionality type of the Policy.

  Member Types

  `pre` - Indicates that the Policy will be evaluated prior to the corresponding function's execution.

  `post` - Indicates that the Policy will be evaluated after the corresponding function's execution.
&nbsp;

#### Modifiers

None.

&nbsp;

#### Events

None.

&nbsp;

#### Public State Variables

None.

&nbsp;

#### Public Functions

`function rule(bytes4 sig, address[5] addresses, uint[3] values, bytes32 identifier) external view returns (bool)`

This view function is called by the PolicyManager to ensure that specific registered function calls are validated by their respective registered Policy validation logic. The function returns `true` when conditions defined in the Policy are met and execution continues to successful completion. If conditions defined in the Policy are no met, the function returns `false`, all execution up to that point is reverted and execution cannot complete, returning all remaining gas.

The `rule()` function takes a function signature hash `sig`, an array of address `addresses`, an array of uint `values` and an `identifier` are passed as parameters. For the arrays, the order of the address or value in the array is semantically significant. The individual array positions, [n], are defined as follows:

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

`function position() external view returns (Applied)`

This view function returns the enum `Applied` which is defined for each specific Policy contract. The `Applied` enum indicates whether the Policy logic is applied prior to, or after the corresponding function's execution. This function is called when the Policy contract is registered with the PolicyManager contract.

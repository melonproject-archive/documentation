# Overview

The Melon protocol can be seen as having two layers.
One is of all the funds in existence, controlled by their respective managers, and participated in by investors.
The second is of infrastructure-level contracts, managed by our governance system, and critical to maintaining a healthy ecosystem for funds.

Each of the subsystems mentioned below are discussed in great detail elsewhere in the documentation.
This article is meant to provide a high-level view of how the components are arranged, and a brief description of what each one does.


## The fund

Each fund is a small constellation of smart contracts, each customized with parameters that the manager desires at creation time.

At the core of the fund is a contract called the Hub.
This contract tracks the rest of the components, which can be thought of as Spokes.
The Hub also provides the methods necessary for setup of the fund, and maintains an access control list of which components can call which methods.

The Vault component functions to simply store tokens on behalf of the fund, and segregate them from the rest of the components as a way to reduce surface area.

One of these other components is the Shares component, which provides a unit of account for ownership in a fund.
Shares are not tradeable, but are created and destroyed as the fund is entered and exited by investors.

This entrypoint is provided by the Participation component.
The functions on that contract allow investors to buy shares with one of some set of allowed assets, or to redeem them for a proportional "slice" of the fund's underlying assets.
Share price determines the amount of shares created for a new investor, and the quantities of underlying assets given at redemption.

The Accounting component provides this overall share price for the fund.
It also tracks amounts of assets owned by the fund, and computes other metrics related to the share price (e.g. gross asset value).
Furtermore, the Accounting component provides a method to trigger fee payment on the fund.

Fees are rewarded to the manager at certain points during the lifetime of a fund, such as on redemption of shares, and at the end fo a designated reward period.
There are currently two types of fees: management and performance fees, and they are tracked by the FeeManager component.
Management fees are calculated based on time only, while the amount of performance fees given is determined by share price evolution.

A manager tries to maximize their fund's performance by making favourable trades, using methods on the Trading component.
This contract provides a transparent way to interact with multiple exchange types, through its use of exchange adapters.

Managers are restricted from making bad trades beyond a certain threshold however, through rules implemented as part of the fund's risk management subsystem.
These rules are also use to mitigate other malicious or negligent behaviour on behalf of the manager.
The risk management rules are tracked in the Policy Manager component.
Incidentally, investment in the fund via the Participation contract is also parameterized by rules called Compliance policies, which are also tracked by the Policy Manager.

While a fund's behaviour is defined in its component contracts, much of its functionality is dependent on having working infrastructure surrounding it.

## Infrastructure contracts

These contracts are deployed for the benefit of the entire network.
They function to keep the entire system running, but are not controlled by individual fund managers or investors.
Instead, they are managed by the governance system, or some component thereof.

The fund factory is deployed for managers to create their funds on a particular protocol version.
Factories for each of the types of fund component are also deployed independently, and are leveraged by the fund factory when producing new funds.

Each exchange type also has a corresponding infrastructure *adapter* contract, which is shared by all funds.
The adapter simply converts "generic" exchange methods on a fund's Trading component to methods that particular exchanges expect.

The Registry contract provides a high-level interface for what other infrastructure contracts know about.

One of the addresses tracked is that of the Engine contract.
The Engine uses a buy-and-burn model discussed elsewhere in the documentation, taking MLN out of circulation by buying it for ETH.
The ETH used is accumulated by the Engine as funds pay for and perform certain actions.

Another contract which is tracked by the Registry is the Price Source for the entire system.
The Price Source is critical to the proper operation of funds, since it yields information used in many of a fund's actions, such as investment, redemption and fee payment.

As mentioned above, the infrastructure layer is managed by Governance.
More information on how governance is structured and operated is described in the Governance documentation. 

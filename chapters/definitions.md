# Definitions

#### Melon Fund
A Melon Fund is an Ethereum smart contract deployed to the Ethereum blockchain.  It is the core smart contract of the Melon Protocol. The Melon Fund has one and only one owner. A specific Melon Fund can be canonically referenced by its contract address. The Melon Fund provides the essential functionality for minimalistic investment management: fund accounting, fee calculation, share issuance, custody, security and the division of responsibilities.

### Stakeholders

#### Owner

#### Investment Manager

#### Investor

#### Exchange

#### Decentralized Exchange (DEX)

#### Price Feed Operator

#### Technical Council

### Actions

#### Redeem

#### Subscribe

#### Emergency Redeem

#### Trade

#### Shutdown

### Terms

#### Slice

#### Shares

#### Price Feed

#### Asset Registrar

#### Governance

#### Risk Engineering

#### Compliance

#### Custody/Custodian

#### Administration/Administrator

#### Audit/Auditor

#### Order

#### Order Book

#### Best Price Execution

#### Management Fee

#### Performance Fee

#### Highwatermark

#### Crystalization Period

#### Hurdle

#### Breach

#### Stake

#### Open-Ended Fund

#### Token

#### ERC20 Standard

#### Ethereum

#### Blockchain

#### Transaction

#### NAV

#### GAV

#### Investment Asset

#### Redemption Asset

#### Quote Asset

#### allocate vs invest
Just a note: should we try to tighten up our language to avoid ambiguity?:  

Allocate: The Investment Manager chooses and trades for specific assets within the fund. Allocation is the only discretionary power with the Investment Manager regarding the Melon Fund's underlying holdings.

Invest: The Investor transfers tokens to the Melon Fund. Technically, the Melon Fund also invests into the underlying holdings; this underscores the fact that the underlying holdings' ownership is completely segregated from the Investment Manager and resided with the Melon Fund smart contract, and ultimately, with the Investor.

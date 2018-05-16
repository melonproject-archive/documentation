---
layout: page
title: Manager
---

# Manager

#### Overview
The Manager (Investment Manager) is the central actor in the Melon Fund. The Investment Manager is the creator of the Melon Fund and as such, the `owner` of the Melon Fund smart contract. In this capacity, the Investment Manager designs the initial set up of the Melon Fund, including:

- Compliance rules regarding Investor participation
- Risk Engineering rules regarding Investments guidelines
- Management and Performance fees
- Exchanges to be used
- Asset Tokens eligible for Subscription and Redemption
- Fund name

Once the Melon Fund is set up, it will be open for investment from Investors. When Investors transfer investment capital to the Melon Fund, the Investment Manager has full discretionary investment allocation authority over the assets, although Investors retain ultimate control over their respective shares in the Melon Fund. The discretionary investment allocation authority is, however, constrained by those Risk Engineering rules put in place by the Investment Manager during fund set up.

The critical point about a Melon Fund is that Investors retain control of their investment in the fund, while delegating asset management activity to the Investment Manager, who has the fine-grained trade and asset management authority, but no ability to remove asset tokens from the segregated safety of the Melon Fund's smart contact custody.

#### Fund Interaction
The Investment Manager in the capacity of  `owner` of the Melon Fund smart contract can as the ability to interact with the following smart contract functions:

- enableInvestment()

- disableInvestment)()

- enableRedemption()

- disableRedemption()  

- shutDown() - Fund shutdown

- callOnExchange() - Make-, Take-, and Cancel Order

Please refer to the Fund documentation for further details on these functions.

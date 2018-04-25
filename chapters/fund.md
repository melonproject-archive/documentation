---
layout: page
title: Fund
---
# Fund

The core functionality of the Melon Protocol is captured by the concept of a "Fund". This functionality is specified in fund.sol.

The Melon Fund is responsible for, and performs:
- Asset Custody
- Performance Accounting
- Fee Accounting
- Shareholder Accounting
- Exchange Integration
- Market Price Data Feed Integration
- Trade Execution

At fund instantiation, the fund creator (Investment Manager, "owner") may discretionarily elect to activate Compliance and/or Risk Engineering functionality. This enables the fund to constrain Investor or Investment Manager interaction in specific and configurable ways.

#### Asset Custody
In the context of a Melon Fund, Assets are cryptographic Tokens [link] adhering to the ERC20 standard [link] on the Ethereum blockchain.  The Asset universe for Melon Funds necessarily requires a that a liquid market exists for the token on one or more decentralized exchanges [link] in order to facilitate price feed and trade execution functionality. The Technical Council [link] shall determine membership of specific Tokens in the Melon Asset universe.

In the legacy financial system, assets are held by custodian banks.

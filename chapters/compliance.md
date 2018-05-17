# Compliance

"Compliance" is a modular smart contract in the Melon Protocol. The purpose of the Compliance module is to provide means to control capital inflows (Melon Fund Subscriptions) from prospective Investors.

## Investment Compliance vs. Trade Compliance
In the Melon Protocol, the term "Compliance" revolves around the specifics of investing as an Investor *into* Melon Fund. This is often referred to as a "Subscription". Details around which addresses, amounts and when Investors may subscribe to the Melon Fund are configured in-, and managed by the Compliance module. To clarify, in the traditional investment management industry, "compliance" is also used to refer to trading of underlying fund assets, as in a whether a certain asset or trade is compliant with portfolio guidelines, laws and regulations. This type of intra-portfolio asset level compliance is handled by the Risk Engineering module in the Melon Protocol.

## Compliance Set up
It is the responsibility and decision of the Investment Manager to determine the requirement, if any, for compliance in the context of the Melon Fund Compliance module. At fund set up, the Investment Manager will specify rules to which the Melon Fund will adhere. Such rule specification will be achieved through the Manager Interface.

## Current Compliance
The main use case for the Melon Fund Compliance module is the ability to create and maintain a whitelist for specific addresses.  That is, the Investment Manager can explicitly define specific addresses which will then have the ability to subscribe to the Melon Fund.

Note that any listing of an address on the Compliance module whitelist only impacts the Investor's *ability* to subscribe to the Melon Fund. An existing investment from a specific address will remain invested irrespective of any change in that address's whitelist status.


## Future Directions
The Melon Fund Compliance module potentially has great significance for regulators and regulatory frameworks. Possible use cases are: Regulators or KYC/AML providers may create and maintain their own whitelist of Investor addresses in a Melon Compliance module that individual Melon Funds could query; the ability for the Investment Manager to "Hard Close" or "Soft Close" subscriptions; the ability to cap subscription amounts.

Hard Close - A fund refuses all new investment subscriptions, usually due to capacity limitations for a specific strategy.

Soft Close - A fund refuses investment subscriptions new Investors, but accepts investment top-ups from existing Investors (addresses).

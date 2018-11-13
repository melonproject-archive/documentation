# Melon Engine: buy-and-burn model

For context, please read Melonomics 2: https://medium.com/melonport-blog/melonomics-part-2-the-melon-engine-48bcb0dae65. 

The Melon Engine is a contract that collects fees paid in ETH for asset management gas units by the users, buys MLN at a premium with the collected ETH and burns MLN. The Melon Engine mechanisms enable the alignement of the token value with the usage of network, while avoiding the pitfalls of high velocity token models. 

## Perspective of a user interacting with the Melon protocol

When a user interacts with an amgu (asset management gas unit) payable function on the Melon protocol, it pays asset management gas in ETH to the Melon network. We are still in the process of defining the functions that are amgu payable. As it stands, the following functions are amgu payable: `setupFund`, `requestInvestment`, `executeRequest` and `claimFees`.

A modifier `amguPayable` is added to certain functions on the Melon protocol. That modifier will apply the logic for the user to pay asset management gas.

The total asset management gas paid is equal to: Number of amgu consumed * amgu price 

The initial amgu price will be set in MLN terms by Melonport AG before deployment in February. It will then be in the hands of the Melon Council to adjust the amgu price when necessary. According to our modeling, the Melon gas price (amgu price) should not need frequent adjustment. The 2 scenarios where we can expect an adjustment would be: 
- Overnight spike in network usage
- Extreme volatility on MLN/USD 

The Melon gas price should always be kept at levels at which it makes the Melon protocol competitive to other asset management alternatives. At the same time, the cost incurred must be sufficient to incentivize maintainers and developers compensated in MLN tokens.


## Melon Engine mechanics 

The ETH from the amgu fees paid by users is then sent to the Melon Engine. 

The Melon Engine contract is a unidirectional liquidity contract that sells ETH and buys MLN at a premium over market price (market price is retrieved through Kyber network). 

The ETH received by the Melon Engine is frozen for a thawing delay of 1 month. After 1 month, the function `thaw` is publicly callable. Calling that function will render the frozen ETH liquid and available for external actors to buy ETH in exchange for MLN. 

As soon as the Melon Engine sells ETH and buys MLN, it burns the acquired MLN. 

The premium schedule provided by the Melon Engine is as follows:
- Ether in the Melon Engine < 1 ETH, premium = 0%
- 1≤ Ether in the Melon Engine < 5, premium = 5%
- 5 ≤ Ether in the Melon Engine < 10, premium = 10%
- Ether in the Melon Engine ≥ 10, premium = 15%


## Perspective of an actor interacting with the Melon Engine

The Melon Engine premium is reserved to Melon fund. Any Melon Fund is eligible to call the `sellAndBurnMln` function on the Melon Engine, and therefore benefit from the premium offered.

**Use case**: As soon as there are liquid ETH in the Melon Engine, a Melon Fund can decide to sell MLN to the Melon Engine for ETH. This is done through the Melon Engine exchange adapter. 

Assume a fund who owns 10 MLN. 
- Through the exchange adapter, the Melon fund calls `sellAndBurnMln(10)`. 
- That function gets the Melon Engine price (ie number of ETH the melon engine is willing to sell for 1 MLN, premium included)
- Transfer to the Melon Engine contract the 10 MLN tokens the Melon fund wants to sell 
- Sends to the Melon fund (10 * Melon Engine price) ETH in exchange for the MLN sold
- Burns the MLN collected in that transaction
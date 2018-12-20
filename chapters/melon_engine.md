# water<b>melon</b> Engine: buy-and-burn model

For context, please read Melonomics 2: https://medium.com/melonport-blog/melonomics-part-2-the-melon-engine-48bcb0dae65. 

The water<b>melon</b> Engine is a contract that collects fees paid in ETH for asset management gas units by the users, buys MLN at a premium with the collected ETH and burns MLN. The water<b>melon</b> Engine mechanisms enable the alignement of the token value with the usage of network, while avoiding the pitfalls of high velocity token models. 

## Perspective of a user interacting with the water<b>melon</b> protocol

When a user interacts with an amgu (asset management gas unit) payable function on the water<b>melon</b> protocol, it pays asset management gas in ETH to the water<b>melon</b> network. We are still in the process of defining the functions that are amgu payable. As it stands, the following functions are amgu payable: `setupFund`, `requestInvestment`, `executeRequest` and `claimFees`.

A modifier `amguPayable` is added to certain functions on the water<b>melon</b> protocol. That modifier will apply the logic for the user to pay asset management gas.

The total asset management gas paid is equal to: Number of amgu consumed * amgu price 

The initial amgu price will be set in MLN terms by Melonport AG before deployment in February. It will then be in the hands of the water<b>melon</b> Council to adjust the amgu price when necessary. According to our modeling, the water<b>melon</b> gas price (amgu price) should not need frequent adjustment. The 2 scenarios where we can expect an adjustment would be: 
- Overnight spike in network usage
- Extreme volatility on MLN/USD 

The water<b>melon</b> gas price should always be kept at levels at which it makes the water<b>melon</b> protocol competitive to other asset management alternatives. At the same time, the cost incurred must be sufficient to incentivize maintainers and developers compensated in MLN tokens.


## water<b>melon</b> Engine mechanics 

The ETH from the amgu fees paid by users is then sent to the water<b>melon</b> Engine. 

The water<b>melon</b> Engine contract is a unidirectional liquidity contract that sells ETH and buys MLN at a premium over market price (market price is retrieved through Kyber network). 

The ETH received by the water<b>melon</b> Engine is frozen for a thawing delay of 1 month. After 1 month, the function `thaw` is publicly callable. Calling that function will render the frozen ETH liquid and available for external actors to buy ETH in exchange for MLN. 

As soon as the water<b>melon</b> Engine sells ETH and buys MLN, it burns the acquired MLN. 

The premium schedule provided by the water<b>melon</b> Engine is as follows:
- Ether in the water<b>melon</b> Engine < 1 ETH, premium = 0%
- 1≤ Ether in the water<b>melon</b> Engine < 5, premium = 5%
- 5 ≤ Ether in the water<b>melon</b> Engine < 10, premium = 10%
- Ether in the water<b>melon</b> Engine ≥ 10, premium = 15%


## Perspective of an actor interacting with the water<b>melon</b> Engine

The water<b>melon</b> Engine premium is reserved to water<b>melon</b> fund. Any water<b>melon</b> Fund is eligible to call the `sellAndBurnMln` function on the water<b>melon</b> Engine, and therefore benefit from the premium offered.

**Use case**: As soon as there are liquid ETH in the water<b>melon</b> Engine, a water<b>melon</b> Fund can decide to sell MLN to the water<b>melon</b> Engine for ETH. This is done through the water<b>melon</b> Engine exchange adapter. 

Assume a fund who owns 10 MLN. 
- Through the exchange adapter, the water<b>melon</b> fund calls `sellAndBurnMln(10)`. 
- That function gets the water<b>melon</b> Engine price (ie number of ETH the water<b>melon</b> engine is willing to sell for 1 MLN, premium included)
- Transfer to the water<b>melon</b> Engine contract the 10 MLN tokens the water<b>melon</b> fund wants to sell 
- Sends to the water<b>melon</b> fund (10 * water<b>melon</b> Engine price) ETH in exchange for the MLN sold
- Burns the MLN collected in that transaction
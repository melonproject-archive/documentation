# Assets

Token assets are the underlying constituent members of a water<b>melon</b> Fund. These token assets are exclusively Ethereum blockchain tokens implementing the ERC20 standard token interface.

## Asset Eligibility

In its governance capacity, the Technical Council determines the water<b>melon</b> Fund asset universe, in that it periodically analyzes the broader Ethereum token universe, seeking eligible tokens given their exchange membership, liquidity and underlying use in the context of asset management.

Technically, a water<b>melon</b> Fund can hold any ERC20 complaint token. However, not all ERC20 compliant tokens are appropriate for water<b>melon</b> Funds to hold due to lack of a market, current pricing, exchange listing, liquidity, etc.

## ETH is not ERC20

It should be noted the the Ethereum native asset, ETH, does _not_ comply with the ERC20 token standard. For this reason, market observers may occasionally see "Wrapped ETH" or "WETH", where ETH is itself held in an ERC20 compliant wrapper contract, so as to have the same behavior when participating in smart contract infrastructure that expects the ERC20 interface. As such, the water<b>melon</b> Fund cannot hold native ETH, but can only hold WETH. This has no impact on the valuation of ETH and will function and be valued like-for-like in the water<b>melon</b> Fund.

## Note on Gas

It should be understood that non-view function interaction with an Ethereum smart contract requires the payment of gas for processing capacity. Gas is priced (dynamically according to network load and capacity) in ETH, so some amount of ETH in a calling address is required. Depending on the complexity and computational intensity of a smart contract, it is possible that non-trivial amounts of ETH may be required.

Please refer to the Asset Registrar for more details.

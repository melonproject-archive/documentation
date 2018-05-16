# Assets

Assets are the underlying constituent members of a Melon Fund. Assets are exclusively Tokens on the Ethereum blockchain implementing the ERC20 or ERC223 standard token interfaces.

The Technical Council determines the asset universe through the [asset registrar](https://github.com/melonproject/documentation/blob/first-draft/chapters/asset_registrar.md) for Melon Funds in that it periodically analyzes the eligibility for tokens given their exchange membership, liquidity and underlying use.

## Fund is an Asset

A Fund is basically an ERC223 compliant asset with super powers. It is a structure which holds assets and seeks to fairly distribute ownership of the collective value of the assets. Since fund is also an asset, a fund can hold different fund shares as it's holdings just like any other normal asset.

Fund keeps track of all the assets it holds using a variable called ownedAssets.

## Fund Assets limit

Due to gas constraints of Ethereum network, there is a limit on the number of assets a fund can hold. This is calculated by performing gas price analysis and basically make sure no function exceeds the gas limit.

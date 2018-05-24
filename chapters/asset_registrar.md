
# Asset Registrar

The Asset Registrar is a smart contract which supports all deployed, live Melon Funds. Its purpose is to enumerate all investment-eligible assets tokens for Melon Funds. Subsequent modules check if an  asset is registered before performing different  operations.  

The Asset Registrar stores data about each investment-eligible asset on-chain.

## Mapping
The Asset Registrar holds the following public mapping:

  `mapping (address => Asset) public information;`

## Asset Definition
The Asset Registrar is primarily a smart contract to hold and provide maintenance infrastructure for a list of investment-eligible assets.  Assets have the following characteristics:

| Name | Type | Description |
| -------- | -------- | -------- |
| `ofAsset` | `address` | The contract address of the token Asset |
| name | bytes32 | Human-readable name of the Asset as found in the ERC223 token standard |
| symbol | bytes8 | Human-readable symbol of the Asset as found in ERC223 token standard |
| decimal | uint | Precision specified as the number of decimals |
| url | string | url address for Asset's extended information |
| ipfsHash | string  | ipfs  address for Asset's extended information |
| chainId | bytes32 | Chain where the asset resides |
|breakIn | address | Address  of  break-in  contract  on destination chain |
| breakOut | address | Address  of  break-out contract on the native chain |



Future versions may define in a standardized way, asset classification or type attributes (or an array of attributes).

## Function Interaction
The `owner` can interact with the Asset Registrar in the following ways:

- `register()`

- `updateDescriptiveInformation()`

- `remove()`

The public can view-interact with the Asset Registrar in the following ways:

- `getName()`

- `getSymbol()`

- `getDecimals()`

# Asset Registrar

Asset  registrar  module  is  intended  for  registering  assets  and information associated with them on-chain. Subsequent modules  check  if  an  asset  is  registered  in  the  Asset  registration before  performing  different  operations.  Registration  can  be done via the register function that takes the following parameters:

| Name | Type | Description |
| -------- | -------- | -------- |
| ofAsset ofFund??| address | A human-readable name of the fund|
| name | string | Human-readable name of the Asset as in ERC223 token standard |
|symbol | string | Human-readable symbol of the As-set as in ERC223 token standard |
|decimal | uint | Number of decimals for precision |
|url | string | For extended information of the assetn
|  ipfsHash | bytes32  | Same as url but for ipfs  |
| chainId | bytes32 | Chain where the asset resides |
|breakIn | address | Address  of  break  in  contract  on destination chain |
| breakOut | address | Address  of  break  out  contract  on this chain |

Since storage on-chain is expensive, a url and/or ipfsHash of  the  document  containing  asset  information  is  stored.  An ipfsHash is preferred however, to ensure data integrity. Due   to   gas   limit   constraints,   the   constant field named maxAssets defines  the  maximum  number  of  assets  that  can be  registered.  This  number  is  calculated  by  checking  the maximum number of registered assets redeemOwnedAssets function supports. This specific function is chosen because it is  the  most  gas  intensive  operation  that  grows  linearly  with the number of registered assets. maxAssets:  (blockGasLimit-rGas1)  /∆(rGasn,rGasn−1]whererGasnis   the   worst-case   gas   consumed   by   redeemOwnedAssets function for ’n’ registered assets.

------

The Asset Registrar is a smart contract which supports the entire population of deployed, live Melon Funds. Its purpose is to enumerate all investment-eligible Assets (Tokens) for Melon Funds.

The Asset Registrar stores data about each investment-eligible asset on-chain.

The Asset Registrar holds the following public mapping:

  mapping (address => Asset) public information;

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

add more detail on: AR 1st, then price feed...

Future versions may... [To consider: asset Classification/Type attributes or array of attributes]

The owner can interact with the Asset Registrar in the following ways:

- `register()`

- `updateDescriptiveInformation()`

- `remove()`

The public can view-interact with the Asset Registrar in the following ways:

- `getName()`

- `getSymbol()`

- `getDecimals()`

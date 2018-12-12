# Registry

## General

The Registry contract is a singleton contract for all funds deployed under a specific Melon protocol version.
&nbsp;

## Registry.sol

#### Description

The Registry contract stores, manages and provides functionality to maintain all relevant data pertaining to individual asset tokens and individual exchanges eligible for investment and operation within a specific Melon Protocol version. The contract provides functionality to define, add, update or remove asset tokens and exchanges.
&nbsp;

#### Inherits from

DSAuth (link)
&nbsp;

#### On Construction

None.
&nbsp;

#### Structs

`Asset`

  Member variables:  

  `bool exists` - A boolean to conveniently and clearly indicate existence in a mapping.
  `string name` - The human-readable name of the asset token as per the ERC223 token standard.
  `string symbol` - The human-readable symbol of the asset token as per ERC223 token standard.
  `uint decimals` -  The divisibility precision of the token as per the ERC223 token standard.
  `string url` - The URL for extended information of the asset token.
  `string ipfsHash` - The IPFS path for extended information of the asset token.
  `address breakIn` - The address of the break-in contract on the destination chain.
  `address breakOut` - The address of the break-out contract on the destination chain.
  `uint[] standards` - An array of integers representing EIP standards to which this asset token conforms.
  `bytes4[] sigs` - An array of asset token function signatures which have been whitelisted.


This struct stores all relevant information pertaining to the asset token.
&nbsp;

`Exchange`

  Member variables:  

  `bool exists` - A boolean to conveniently and clearly indicate existence in a mapping.
  `address adapter` - The address of the exchange's corresponding adapter contract registered.
  `bool takesCustody` - A boolean indicating the custodial nature of the exchange.
  `bytes4[] sigs` - An array of exchange function signatures which have been whitelisted.

This struct stores all relevant information pertaining to the exchange.
&nbsp;

#### Enums

None.

&nbsp;

#### Modifiers

None.

&nbsp;


#### Events

`AssetUpsert()`

  `address indexed asset` - The address of the asset token contract registered.
  `string name` - The human-readable name of the asset token as per the ERC223 token standard.
  `string symbol` - The human-readable symbol of the asset token as in ERC223 token standard
  `uint decimals` - The divisibility precision of the token as per the ERC223 token standard.
  `string url` - The URL for extended information of the asset token.
  `string ipfsHash` - The IPFS path for extended information of the asset token.
  `address[2] breakInBreakOut`- An array of addresses of break-in and break-out contracts on the destination chain. This is meant for potential intra-chain asset holdings.
  `uint[] standards` - An array of integers representing EIP standards to which this asset token conforms.
  `bytes4[] sigs` - An array of asset token function signatures which have been whitelisted.

This event is triggered when an asset is added or updated in `registeredAssets`. The parameters listed above are provided with the event.
&nbsp;

`event ExchangeUpsert()`

  `address indexed exchange` - The address of the exchange contract registered.
  `address indexed adapter` - The address of the exchange's corresponding adapter contract registered.
  `bool takesCustody` - A boolean indicating the custodial nature of the exchange.
  `bytes4[] sigs` - An array of exchange function signatures which have been whitelisted.

This event is triggered when an exchange is added or updated in `registeredExchanges`. The parameters listed above are provided with the event.
&nbsp;

`event AssetRemoval()`

  `address indexed asset` - The address of the registered asset token contract to be removed.

This event is triggered when an asset is removed from the `registeredAssets` array state variable. The parameters listed above are provided with the event.
&nbsp;

`event ExchangeRemoval()`

  `address indexed exchange` - The address of the registered exchange contract to be removed.

This event is triggered when an exchange is removed from the `registeredExchanges` array state variable. The parameters listed above are provided with the event.
&nbsp;

#### Public State Variables

`mapping (address => Asset) public assetInformation`

This public state variable mapping maps an asset token contract `address` to an `Asset` strut containing the asset information described above.
&nbsp;

`address[] public registeredAssets`

This public state variable is an array of addresses which stores each asset token contract `address` which is registered.
&nbsp;

`mapping (address => Exchange) public exchangeInformation`

This public state variable mapping maps an exchange contract `address` to an `Exchange` strut containing the exchange information described above.
&nbsp;

`address[] public registeredExchanges`

This public state variable is an array of addresses which stores each exchange contract `address` which is registered.
&nbsp;

#### Public Functions

`function registerAsset(
        address _asset,
        string _name,
        string _symbol,
        uint _decimals,
        string _url,
        string _ipfsHash,
        address[2] _breakInBreakOut,
        uint[] _standards,
        bytes4[] _sigs
    ) auth`

This function requires that the caller is the `owner` or the current contract. It then requires that the asset token's information not be previously registered. The asset token's address is then appended to the `registeredAssets` array state variable. The function then registers the following information for a specific asset token within the Registry contract as per the following parameters:

`address _asset` - The address of the asset token contract to be registered.
`string _name` - The human-readable name of the asset token as per the ERC223 token standard.
`string _symbol` - The human-readable symbol of the asset token as in ERC223 token standard
`uint _decimals` - The divisibility precision of the token as per the ERC223 token standard.
`string _url` - The URL for extended information of the asset token.
`string _ipfsHash` - The IPFS path for extended information of the asset token.
`address[2] _breakInBreakOut`- An array of addresses of break-in and break-out contracts on the destination chain. This is meant for potential intra-chain asset holdings.
`uint[] _standards` - An array of integers representing EIP standards to which this asset token conforms.
`bytes4[] _sigs` - An array of asset token function signatures which have been whitelisted.

Finally, the function ensures that the asset token's information exists in the `assetInformation` mapping state variable.
&nbsp;

`function registerExchange(
    address _exchange,
    address _adapter,
    bool _takesCustody,
    bytes4[] _sigs
) auth`

This function requires that the caller is the `owner` or the current contract. It then requires that the exchange's information not be previously registered. The exchange's address is then appended to the `registeredExchanges` array state variable. The function then registers the following information for a specific exchange within the Registry contract as per the following parameters:

`address _exchange` - The address of the exchange contract to be registered.
`address _adapter` - The address of the exchange's corresponding adapter contract to be registered.
`bool _takesCustody` - A boolean indicating the custodial nature of the exchange.
`bytes4[] _sigs` - An array of exchange function signatures which have been whitelisted.

Finally, the function ensures that the exchange's information exists in the `exchangeInformation` mapping state variable.
&nbsp;

`function updateAsset(
    address _asset,
    string _name,
    string _symbol,
    uint _decimals,
    string _url,
    string _ipfsHash,
    address[2] _breakInBreakOut,
    uint[] _standards,
    bytes4[] _sigs
) auth`

This function requires that the caller is the `owner` or the current contract. It then requires that the asset token's information be previously registered. The function then updates the following information for a specific asset token within the Registry contract as per the following parameters:

`address _asset` - The address of the asset token contract to be registered.
`string _name` - The human-readable name of the asset token as per the ERC223 token standard.
`string _symbol` - The human-readable symbol of the asset token as in ERC223 token standard
`uint _decimals` - The divisibility precision of the token as per the ERC223 token standard.
`string _url` - The URL for extended information of the asset token.
`string _ipfsHash` - The IPFS path for extended information of the asset token.
`address[2] _breakInBreakOut`- An array of addresses of break-in and break-out contracts on the destination chain. This is meant for potential intra-chain asset holdings.
`uint[] _standards` - An array of integers representing EIP standards to which this asset token conforms.
`bytes4[] _sigs` - An array of asset token function signatures which have been whitelisted.

Finally, the function emits the `AssetUpsert` event along with the parameters listed above.
&nbsp;

`function updateExchange(
    address _exchange,
    address _adapter,
    bool _takesCustody,
    bytes4[] _sigs
) auth`

This function requires that the caller is the `owner` or the current contract. It then requires that the exchange's information be previously registered. The function then updates the following information for a specific exchange within the Registry contract as per the following parameters:

`address _exchange` - The address of the exchange contract to be registered.
`address _adapter` - The address of the exchange's corresponding adapter contract to be registered.
`bool _takesCustody` - A boolean indicating the custodial nature of the exchange.
`bytes4[] _sigs` - An array of exchange function signatures which have been whitelisted.

Finally, the function emits the `ExchangeUpsert` event along with the parameters listed above.
&nbsp;


`function removeAsset(address _asset, uint _assetIndex) auth`

This function requires that the caller is the `owner` or the current contract. The function requires the following parameters:

`address _asset` - The address of the asset token contract to be removed.
`uint _assetIndex` - The index of the asset provided in the registerAssets array state variable.

The function then requires that the asset information exists and that the asset is registered. The function then deletes the asset's entries from the `assetInformation` mapping state variable and the `registeredAssets` array state variable, while also maintaining the `registeredAssets` array. The function ensures that the asset's information entry no longer exists and finally emits the `AssetRemoval()` event with the asset's token contract address.
&nbsp;

`function removeExchange(address _exchange, uint _exchangeIndex) auth`

This function requires that the caller is the `owner` or the current contract. The function requires the following parameters:

`address _exchange` - The address of the exchange contract to be removed.
`uint _exchangeIndex` - The index of the exchange provided in the registerAssets array state variable.

The function then requires that the exchange information exists and that the exchange is registered. The function then deletes the exchange's entries from the `exchangeInformation` mapping state variable and the `registeredExchanges` array state variable, while also maintaining the `registeredExchanges` array. The function ensures that the exchange's information entry no longer exists and finally emits the `ExchangeRemoval()` event with the exchange's contract address.
&nbsp;

`function getName(address _asset) view returns (string)`

This public view function retrieves the asset token `name` for the registered asset address provided.
&nbsp;

`function getSymbol(address _asset) view returns (string)`

This public view function retrieves the asset token `symbol` for the registered asset address provided.
&nbsp;

`function getDecimals(address _asset) view returns (uint)`

This public view function retrieves the asset token `decimals` for the registered asset address provided. `decimals` specifies the divisibility precision of the token.
&nbsp;

`function assetIsRegistered(address _asset) view returns (bool)`

This public view function indicates whether the address provided is that of a registered asset token. A return value of `true` indicates that the asset is registered. A return value of `false` indicates that the asset is not registered.
&nbsp;

`function getRegisteredAssets() view returns (address[])`

This public view function returns an array of all registered asset token addresses.
&nbsp;

`function assetMethodIsAllowed(address _asset, bytes4 _sig) returns (bool)`

This public view function returns a boolean indicating whether a specific asset token function is whitelisted given the provided asset token address and the function's signature hash. A return value of `true` indicates that the function is whitelisted. A return value of `false` indicates that the function is not whitelisted.
&nbsp;

`function exchangeIsRegistered(address _exchange) view returns (bool)`

This public view function returns a boolean indicating whether a specific exchange is registered. A return value of `true` indicates that the exchange is registered. A return value of `false` indicates that the exchange is not registered.
&nbsp;

`function getRegisteredExchanges() view returns (address[])`

This public view function returns an array of all registered exchange addresses.
&nbsp;

`function getExchangeInformation(address _exchange) view returns (address, bool)`

This public view function returns the corresponding exchange adapter contract address and the exchange's boolean indicator `takesCustody` given the provided exchange contract address.
&nbsp;

`function getExchangeFunctionSignatures(address _exchange) view returns (bytes4[])`

This public view function returns an array of whitelisted exchange function signatures corresponding to the provided exchange contract address.
&nbsp;

`function exchangeMethodIsAllowed(address _exchange, bytes4 _sig) returns (bool)`

This public view function returns a boolean indicating whether a specific exchange function is whitelisted given the provided exchange address and the function's signature hash. A return value of `true` indicates that the function is whitelisted. A return value of `false` indicates that the function is not whitelisted.
&nbsp;

# Registry

## General

The Registry contract is a singleton contract for all funds deployed under a specific water<b>melon</b> protocol version.
&nbsp;

## Registry.sol

#### Description

The Registry contract stores, manages and provides functionality to maintain all relevant data pertaining to individual asset tokens and individual exchanges eligible for investment and operation within a specific water<b>melon</b> Protocol version. The contract provides functionality to define, add, update or remove asset tokens and exchanges.
&nbsp;

#### Inherits from

DSAuth (link)
&nbsp;

#### On Construction

The constructor takes the parameter `address _postDeployOwner` to set the owner of registry contract.
&nbsp;

#### Structs

`Asset`

  Member variables:  

  `bool exists` - A boolean to conveniently and clearly indicate existence in a mapping.
  `string name` - The human-readable name of the asset token.
  `string symbol` - The human-readable symbol of the asset token.
  `uint decimals` -  The divisibility precision of the token.
  `string url` - The URL for extended information of the asset token.
  `uint reserveMin` - An integer representing the Kyber Network reserve minimum.
  `uint[] standards` - An array of integers representing EIP standards to which this asset token conforms.
  `bytes4[] sigs` - An array of asset token function signatures which have been whitelisted.


This struct stores all relevant information pertaining to the asset token.
&nbsp;

`Exchange`

  Member variables:  

  `bool exists` - A boolean to conveniently and clearly indicate existence in a mapping.
  `address exchangeAddress` - The address of the exchange contract.
  `bool takesCustody` - A boolean indicating the custodial nature of the exchange.
  `bytes4[] sigs` - An array of exchange function signatures which have been whitelisted.

This struct stores all relevant information pertaining to the exchange.
&nbsp;

Version

  Member variables:  

  `bool exists` -  A boolean to conveniently and clearly indicate existence in a mapping.
  `bytes32 name` - A 32 byte value type to represent the name of the Version.

This struct stores all relevant information pertaining to the Version.
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
  `string name` - The human-readable name of the asset token.
  `string symbol` - The human-readable symbol of the asset token.
  `uint decimals` - The divisibility precision of the token.
  `string url` - The URL for extended information of the asset token.
  `uint reserveMin` - An integer representing the Kyber Network reserve minimum.
  `uint[] standards` - An array of integers representing EIP standards to which this asset token conforms.
  `bytes4[] sigs` - An array of asset token function signatures which have been whitelisted.

This event is triggered when an asset is added or updated in `registeredAssets`. The parameters listed above are provided with the event.
&nbsp;

`event ExchangeAdapterUpsert()`

  `address indexed exchange` - The address of the exchange contract registered.
  `address indexed adapter` - The address of the exchange's corresponding adapter contract registered.
  `bool takesCustody` - A boolean indicating the custodial nature of the exchange.
  `bytes4[] sigs` - An array of exchange function signatures which have been whitelisted.

This event is triggered when an exchange is added or updated in `registeredExchanges`. The parameters listed above are provided with the event.
&nbsp;

`event AssetRemoval()`

  `address indexed asset` - The address of the registered asset token contract to be removed.

This event is triggered when an asset is removed from the `registeredAssets` array state variable. The parameter listed above are provided with the event.
&nbsp;

`event ExchangeAdapterRemoval()`

  `address indexed exchange` - The address of the registered exchange contract to be removed.

This event is triggered when an exchange is removed from the `registeredExchanges` array state variable. The parameter listed above are provided with the event.
&nbsp;

`event VersionRegistration()`

  `address indexed version` - The address of the Version registered.

This event is triggered when a Version is registered. The parameter listed above are provided with the event.
&nbsp;

`event IncentiveChange()`

`uint incentiveAmount` - The new incentive amount.

This event is triggered when a price source is changed. The parameter listed above are provided with the event.
&nbsp;

`event PriceSourceChange()`

  `address indexed priceSource` - The address of the new price source.

This event is triggered when a price source is changed. The parameter listed above are provided with the event.
&nbsp;

`event MlnTokenChange()`

  `address indexed mlnToken` - The address of the new MLN token contract.

This event is triggered when the MLN token is migrated to a new contract. The parameter listed above are provided with the event.
&nbsp;

`event NativeAssetChange()`

  `address indexed nativeAsset` - The address of the new native asset token contract.

This event is triggered when the native asset token is migrated to a new contract. The parameter listed above are provided with the event.
&nbsp;

`event EngineChange()`

  `address indexed engine` - The address of the new water<b>melon</b> Engine contract.

This event is triggered when the water<b>melon</b> Engine is migrated to a new contract. The parameter listed above are provided with the event.
&nbsp;

`event EfxWrapperRegistryChange()`

  `address indexed registry` - The address of the new Efx Wrapper Registry contract.

This event is triggered when the water<b>melon</b> Engine is migrated to a new contract. The parameter listed above are provided with the event.
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

`address[] public registeredExchangeAdapters`

This public state variable is an array of addresses which stores each exchange contract `address` which is registered.
&nbsp;

`mapping (address => Version) public versionInformation`

This public state variable mapping maps a Version contract `address` to a `Version` strut containing the Version information described above.
&nbsp;

`address[] public registeredVersions`

This public state variable is an array of addresses which stores each Version contract `address` which is registered.
&nbsp;

`mapping (address => address) public fundsToVersions`

This public state variable mapping maps a Version contract `address` to a water<b>melon</b> fund address.
&nbsp;

`mapping (bytes32 => bool) public versionNameExists`

This public state variable mapping maps the Version name to a boolean indicating the Version name existence.
&nbsp;

`mapping (bytes32 => address) public fundNameHashToOwner`

This public state variable mapping maps the hash of the fund name to the fund owner. A `bytes32` type is used, as dynamic-length types cannot be used as keys in mappings and a hash of the fund name allows for names longer than 32 bytes.
&nbsp;

`uint public constant MAX_REGISTERED_ENTITIES = 20`

This public constant state variable represents the maximum quantity of registered entities and is set to "20". This constant applies to Exchanges registered and Assets registered.
&nbsp;

`uint public constant MAX_FUND_NAME_BYTES = 66`

This public constant state variable represents the maximum water<b>melon</b> fund name size in bytes. The maximum is set to 66 bytes.
&nbsp;

`address public priceSource`

This public state variable is the address of the current, active price source contract.
&nbsp;

`address public mlnToken`

This public state variable is the address of the water<b>melon</b> token contract.
&nbsp;

`address public nativeAsset`

This public state variable is the address of the native asset token contract.
&nbsp;

`address public engine`

This public state variable is the address of the water<b>melon</b> Engine contract.
&nbsp;

`address public ethfinexWrapperRegistry`  

This public state variable is the address of the Ethfinex Wrapper Registry contract.
&nbsp;

`uint public incentive = 10 finney`  

This public state variable is the incentive amount awarded for subscription execution. The amount is set to 10 finney.
&nbsp;

#### Public Functions

`function registerAsset(
        address _asset,
        string _name,
        string _symbol,
        string _url,
        uint _reserveMin,
        uint[] _standards,
        bytes4[] _sigs
    ) external auth`

This function requires that the caller is the `owner` or the current contract. It then requires that the asset token's information not be previously registered. The asset token's address is then appended to the `registeredAssets` array state variable. The function then registers the following information for a specific asset token within the Registry contract as per the following parameters:

`address _asset` - The address of the asset token contract to be registered.
`string _name` - The human-readable name of the asset token.
`string _symbol` - The human-readable symbol of the asset token.
`string _url` - The URL for extended information of the asset token.
`uint reserveMin` - An integer representing the Kyber Network reserve minimum.
`uint[] _standards` - An array of integers representing EIP standards to which this asset token conforms.
`bytes4[] _sigs` - An array of asset token function signatures which have been whitelisted.

Finally, the function ensures that the asset token's information exists in the `assetInformation` mapping state variable.
&nbsp;

`function registerExchangeAdapter(
    address _exchange,
    address _adapter,
    bool _takesCustody,
    bytes4[] _sigs
) external auth`

This external function requires that the caller is the `owner` or the current contract. It then requires that the exchange's information not be previously registered. The exchange's address is then appended to the `registeredExchanges` array state variable. The function then registers the following information for a specific exchange within the Registry contract as per the following parameters:

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
    string _url,
    uint _reserveMin,
    uint[] _standards,
    bytes4[] _sigs
) public auth`

This public function requires that the caller is the `owner` or the current contract. It then requires that the asset token's information be previously registered. The function then updates the following information for a specific asset token within the Registry contract as per the following parameters:

`address _asset` - The address of the asset token contract to be registered.
`string _name` - The human-readable name of the asset token.
`string _symbol` - The human-readable symbol of the asset token.
`string _url` - The URL for extended information of the asset token.
`uint reserveMin` - An integer representing the Kyber Network reserve minimum.
`uint[] _standards` - An array of integers representing EIP standards to which this asset token conforms.
`bytes4[] _sigs` - An array of asset token function signatures which have been whitelisted.

Finally, the function emits the `AssetUpsert` event along with the parameters listed above.
&nbsp;

`function updateExchangeAdapter(
    address _exchange,
    address _adapter,
    bool _takesCustody,
    bytes4[] _sigs
) public auth`

This public function requires that the caller is the `owner` or the current contract. It then requires that the exchange's information be previously registered. The function then updates the following information for a specific exchange within the Registry contract as per the following parameters:

`address _exchange` - The address of the exchange contract to be registered.
`address _adapter` - The address of the exchange's corresponding adapter contract to be registered.
`bool _takesCustody` - A boolean indicating the custodial nature of the exchange.
`bytes4[] _sigs` - An array of exchange function signatures which have been whitelisted.

Finally, the function emits the `ExchangeAdapterUpsert` event along with the parameters listed above.
&nbsp;


`function removeAsset(address _asset, uint _assetIndex) external auth`

This external function requires that the caller is the `owner` or the current contract. The function requires the following parameters:

`address _asset` - The address of the asset token contract to be removed.
`uint _assetIndex` - The index of the asset provided in the registerAssets array state variable.

The function then requires that the asset information exists and that the asset is registered. The function then deletes the asset's entries from the `assetInformation` mapping state variable and the `registeredAssets` array state variable, while also maintaining the `registeredAssets` array. The function ensures that the asset's information entry no longer exists and finally emits the `AssetRemoval()` event with the asset's token contract address.
&nbsp;

`function removeExchangeAdapter(address _adapter, uint _adapterIndex) external auth`

This function requires that the caller is the `owner` or the current contract. The function requires the following parameters:

`address _exchange` - The address of the exchange contract to be removed.
`uint _exchangeIndex` - The index of the exchange provided in the registerAssets array state variable.

The external function then requires that the exchange information exists and that the exchange is registered. The function then deletes the exchange's entries from the `exchangeInformation` mapping state variable and the `registeredExchangeAdapters` array state variable, while also maintaining the `registeredExchangeAdapters` array. The function ensures that the exchange's information entry no longer exists and finally emits the `ExchangeAdapterRemoval()` event with the exchange's contract address.
&nbsp;

`function getName(address _asset) external view returns (string)`

This external view function retrieves the asset token `name` for the registered asset address provided.
&nbsp;

`function getSymbol(address _asset) external view returns (string)`

This external view function retrieves the asset token `symbol` for the registered asset address provided.
&nbsp;

`function getDecimals(address _asset) external view returns (uint)`

This external view function retrieves the asset token `decimals` for the registered asset address provided. `decimals` specifies the divisibility precision of the token.
&nbsp;

`function assetIsRegistered(address _asset) external view returns (bool)`

This external view function indicates whether the address provided is that of a registered asset token. A return value of `true` indicates that the asset is registered. A return value of `false` indicates that the asset is not registered.
&nbsp;

`function getRegisteredAssets() external view returns (address[])`

This external view function returns an array of all registered asset token addresses.
&nbsp;

`function assetMethodIsAllowed(address _asset, bytes4 _sig) external returns (bool)`

This external view function returns a boolean indicating whether a specific asset token function is whitelisted given the provided asset token address and the function's signature hash. A return value of `true` indicates that the function is whitelisted. A return value of `false` indicates that the function is not whitelisted.
&nbsp;

`function exchangeAdapterIsRegistered(address _exchange) external view returns (bool)`

This external view function returns a boolean indicating whether a specific exchange adapter is registered. A return value of `true` indicates that the exchange is registered. A return value of `false` indicates that the exchange is not registered.
&nbsp;

`function getRegisteredExchangeAdapters() external view returns (address[])`

This external view function returns an array of all registered exchange adapter addresses.
&nbsp;

`function getExchangeInformation(address _adapter) view returns (address, bool)`

This public view function returns the corresponding exchange contract address and the exchange's boolean indicator `takesCustody` given the provided exchange contract address.
&nbsp;

`function getAdapterFunctionSignatures(address _adapter) public view returns (bytes4[])`

This public view function returns an array of whitelisted exchange adapter function signatures corresponding to the provided exchange adapter contract address.
&nbsp;

`function adapterMethodIsAllowed(address _adapter, bytes4 _sig) external returns (bool)`

This external view function returns a boolean indicating whether a specific exchange adapter function is whitelisted given the provided exchange adapter address and the function's signature hash. A return value of `true` indicates that the function is whitelisted. A return value of `false` indicates that the function is not whitelisted.
&nbsp;

`function registerVersion(address _version, bytes32 _name) external auth`

This external function requires that the caller is the `owner` or the current contract. The function sets the `versionInformation` mapping `exists` to `true`, `name` to `_name` and pushes the Version address on to the `registeredVersions` array. The `versionNameExists` mapping for this Version name is set to `true`. Finally, the `VersionRegistration()` event is emitted, logging the Version address. Versions cannot be removed from the registry.
&nbsp;

`function setPriceSource(address _priceSource) external auth`

This external function requires that the caller is the `owner` or the current contract. The function sets the `priceSource` state variable to the `_priceSource` parameter value and emits the `PriceSourceChange()` event, logging `_priceSource`.
&nbsp;

`function setMlnToken(address _mlnToken) external auth`

This external function requires that the caller is the `owner` or the current contract. The function sets the `mlnToken` state variable to the `_mlnToken` parameter value and emits the `MlnTokenChange()` event, logging `_mlnToken`.
&nbsp;

`function setNativeAsset(address _nativeAsset) external auth`

This external function requires that the caller is the `owner` or the current contract. The function sets the `nativeAsset` state variable to the `_nativeAsset` parameter value and emits the `NativeAssetChange()` event, logging `_nativeAsset`.
&nbsp;

`function setEngine(address _engine) external auth`

This external function requires that the caller is the `owner` or the current contract. The function sets the `engine` state variable to the `_engine` parameter value and emits the `EngineChange()` event, logging `_engine`.
&nbsp;

`function setIncentive(uint _weiAmount) external auth`

This external function requires that the caller is the `owner` or the current contract. The function sets the `incentive` state variable to `_weiAmount`, then emits the `IncentiveChange()` event logging  `_weiAmount`.
&nbsp;

`function setEthfinexWrapperRegistry(address _registry) external auth`

This external function requires that the caller is the `owner` or the current contract. The function sets the `ethfinexWrapperRegistry` state variable to `_registry`, then emits the `EfxWrapperRegistryChange()` event logging `_registry`. 
&nbsp;

`function getReserveMin(address _asset) external view returns (uint)`

This external view function returns the `reserveMin` for the asset token contract address provided.
&nbsp;

`function exchangeForAdapter(address _adapter) external view returns (address)`

This external view function returns the address of the exchange contract given the exchange adapter contract address provided.
&nbsp;

`function getRegisteredVersions() external view returns (address[])`

This external view function returns an exhaustive array of addresses of all registered Version contracts.
&nbsp;

`function isFund(address _who) external view returns (bool)`

This public view function returns a boolean indicating whether the address provided is a water<b>melon</b> fund contract.
&nbsp;

`function isFundFactory(address _who) external view returns (bool)`

This public view function returns a boolean indicating whether the address provided is a FundFactory. The function check the existence of the `_who` address in the `versionInformation` mapping. Note that Version inherits FundFactory.
&nbsp;

`function registerFund(address _fund, address _owner, string _name) external`

This public function ensures that `msg.sender` is a Version, as only Versions can register funds. The function requires that the fund name is valid. The function then adds an entry to the `fundsToVersions` mapping, associating `_fund` to `msg.sender`, i.e. the Version address. Finally, the function adds an entry to the `fundNameHashToOwner` mapping, associating the keccak256 hash of the fund name to the `owner` address.
&nbsp;

`function isValidFundName(string _name) public view returns (bool)`

This public function ensures that the fund name provided does not exceed `MAX_FUND_NAME_BYTES` nor contain restricted characters. Legal characters are "0-9", "a-z", "A-Z", " ", "-", ".", "\_" and "\*".
&nbsp;

`function canUseFundName(address _user, string _name) public view returns (bool)`

This public function ensures that the fund name provided adheres to the rules set forth in `isValidFundName()`, and that the name is not already in use by a fund or is used by the provided `_user` address (to enable fund name reuse across Versions by the same `_user`).
&nbsp;

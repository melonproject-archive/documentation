# Assets

Assets are the underlying constituent members of a Melon Fund. Assets are exclusively Tokens on the Ethereum blockchain implementing the ERC20 standard token interface.

The Technical Council determines the Asset universe for Melon Funds in that it periodically analyzes the eligibility for tokens given their exchange membership, liquidity and underlying use.

When an asset


address breakIn; // Break in contract on destination chain
address breakOut; // Break out contract on this chain; A way to leave
bytes32 chainId; // On which chain this asset resides
uint decimal; // Decimal, order of magnitude of precision, of the Asset as in ERC223 token standard
bool exists; // Is this asset registered
string ipfsHash; // Same as url but for ipfs
bytes32 name; // Human-readable name of the Asset as in ERC223 token standard
uint price; // Price of asset quoted against `QUOTE_ASSET` * 10 ** decimals
bytes8 symbol; // Human-readable symbol of the Asset as in ERC223 token standard
uint timestamp; // Timestamp of last price update of this asset
string url; // URL for additional information of Asset

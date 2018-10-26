# Fees

## FeeManager.sol

Description
...

Inherits from Spoke, DSMath (link)

On Construction
Sets the hub of the spoke.


Structs
None.

Enums
None.


Public State Variables

`Fee[] public fees`

An array of type fees storing the defined fees.


`mapping (address => bool) public feeIsRegistered`

A mapping storing the registration status of a fee address.

Public Functions

`function register(address feeAddress) public`

This function adds the feeAddress provided to the `Fee[]` array and sets the `feeIsRegistered` mapping for that address to `true`.


`function batchRegister(address[] feeAddresses) public`

This function adds an array of fee addresses provided to the `Fee[]` array and sets the `feeIsRegistered` mappings for those addresses to `true`.


`function totalFeeAmount() public view returns (uint total)`

This function returns the total amount of fees incurred for the hub.


`function rewardFee(Fee fee) public`

This function creates shares commensurate with the fee provided.


`function rewardAllFees() public`

This function creates shares commensurate with all fees stored in the `Fee[]` state variable array.


## FixedManagementFee.sol

Description
...

Inherits from Fee and DSMath (link)

On Construction
No specified behavior.

Structs
None.

Enums
None.

Public State Variables

`uint public PAYMENT_TERM`

An integer defining the measurement period in seconds.


`uint public MANAGEMENT_FEE_RATE`

An integer defining the management fee percentage rate.


`uint public DIVISOR`

An integer defining the standard divisor.


`uint public lastPayoutTime`

An integer defining the block time in UNIX epoch seconds when the previous fee payout was executed.


Public Functions

`function amountFor(address hub) public view returns (uint feeInShares)`

This function calculates and returns the number of shares to be created given the amount of time since the previous fee payment, asset value and the defined management fee rate.


`function updateFor(address hub) external`

This function sets `lastPayoutTime` to the current block timestamp.


## FixedPerformanceFee.sol  

Description
...

Inherits from Fee and DSMath (link)

On Construction
No specified behavior.

Structs
None.

Enums
None.

Public State Variables

`uint public PERFORMANCE_FEE_RATE`

An integer defining the performance fee percentage rate.


`uint public DIVISOR`

An integer defining the standard divisor.


`uint public highWaterMark`

An integer defining the asset value which must be exceeded at the measurement period's end to facilitate the determination of the performance fee due to the manager.


`uint public lastPayoutTime`

An integer defining the block time in UNIX epoch seconds when the previous fee payout was executed.


Public Functions

`function amountFor(address hub) public view returns (uint feeInShares)`

This function calculates and returns the number of shares to be created given the fund performance since the previous measurement period payment, asset value and the defined performance fee rate.


`function updateFor(address hub) external`

This function sets the `highwatermark` and `lastPayoutTime` if applicable.

# Fees

### General

Fees are charges levied against a Melon fund's assets for:

- management services rendered, in the form of Management Fees
- fund performance achieved, in the form of Performance Fees.

Legacy investment funds are typically run as legal vehicle; a business with income and expenses. As a traditional fund incurs expenses (not only those listed above, but others like administration-, custody-, directors-, audit fess), traditional funds must liquidate assets to cash or pay the expenses out of cash held by the fund. This necessarily reduces the assets of the fund and incurs further trading costs.

Melon funds employ a novel and elegant solution: Instead of using cash, or liquidating positions to pay fees, the Melon fund smart contract itself calculates and creates new shares (or share fractions), allocating them to the investment manager's own account holdings within the fund as payment.

At that point, the investment manager can continue holding an increased number of shares in their own fund, or redeem the shares to pay their own operating costs, expand their research activities or whatever else they deem appropriate. The Melon Fund smart contract autonomously and verifiably maintains the shareholder accounting impact in a truly reliable and transparent manner.

Paying fees in this manner has a few interesting side-effects: Assets do not leave the fund, as would a cash payment. There are no unnecessary trading or cash management transactions. Investors and managers have access to real-time fee accrual metrics. Finally, the incentives to the manager are reinforced beyond a cash performance fee by being paid in the currency that is their own product.

The Management Fee and Performance Fee are each represented by an individual contract. The business logic and functionality of each fee is defined within the respective contract. The contract will interact with the various components of the fund to calculate and return the quantity of shares to create. This quantity is then added to the Manager address balance and to the total supply balance.

The Fee Manager component manages the individual fee contracts which have been configured for the Melon fund at set up. The core of the Fee Manager is an array of Fee contract instances. Functions exist to prime this array at fund setup with the selected and configured fee contracts, as well as basic query functionality to aid the user interface in calculating and representing the share NAV.

Fees are calculated and allocated when fund actions such as subscribe, redeem or claim fees are executed by a participant. Calling `rewardAllFees()` will iterate over the array of registered fees in the fee manager, calling each fee's calculation logic. This returns the quantity of shares to create and allocate to the manager.

It is important to note that fee calculations take place before the fund's share quantity is impacted by subscriptions or redemptions. To this end, when a subscription or redemption action is initiated by an investor, the execution order first calculates fee amounts and creates the corresponding share quantity, as the elapsed time and share quantity at the start is known. Essentially, the Melon fund calculates and records a reconciled state immediately and in the same transaction where share quantity changes due subscription/redemption.

##### Management Fees

Management Fees are earned with the passage of time, irrespective of performance. The order of fee calculations is important. The Management Fee share quantity calculation is a prerequisite to the Performance Fee calculation, as fund performance must be reduced by the Management Fee expense to fairly ascertain net performance.

The Management Fee calculation business logic is fully encapsulated by the Management Fee contract. This logic can be represented as follows.

First, the time-weighted, pre-dilution share quantity is calculated:

&nbsp;

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<img src="https://latex.codecogs.com/svg.latex?\Large&space;PD_{f}$=($T_{n}$)($\frac{t_{e}}{t_{y}}$)($f_{m}$)"/>

then, this figure is scaled such that investors retain their original share holdings quantity, but newly created shares represent the commensurate fee percentage amount:

&nbsp;

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<img src="https://latex.codecogs.com/svg.latex?\Large&space;SMF_{e}=\frac{PD_{f}T_{n}}{T_{n}-PD_{f}}$"/>

where,

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<img src="https://latex.codecogs.com/svg.latex?\Large&space;PD_{f}"/> = pre-dilution quantity of shares

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<img src="https://latex.codecogs.com/svg.latex?\Large&space;T_{n}$"/> = current shares outstanding

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<img src="https://latex.codecogs.com/svg.latex?\Large&space;t_{e}$"/> = number of seconds elapsed since previous conversion event

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<img src="https://latex.codecogs.com/svg.latex?\Large&space;t_{y}$"/> = number of seconds in a year ( = 60 _ 60 _ 24 \* 365 )

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<img src="https://latex.codecogs.com/svg.latex?\Large&space;f_{m}$"/> = Management Fee rate

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<img src="https://latex.codecogs.com/svg.latex?\Large&space;SMF_{e}$"/> = number shares to create to compensate Management Fees earned during the conversion period

&nbsp;

##### Performance Fees

Performance Fees accrue over time with performance, but can only be harvested after regular, pre-determined points in time. This period is referred to as the Measurement Period and is decided by the fund manager and configured at fund set up.

Performance is assessed at the end of the Measurement Period by comparing the fund's current share price net of Management Fees to the fund's current high-water mark (HWM).

The HWM represents the highest share valuation which the Melon fund has historically achieved _at a Measurement Period ending time_. More clearly, it is not a fund all-time-high, but rather the maximum share valuation of all Measurement Period-end snapshot valuations.

If the difference to the HWM is positive, performance has been achieved and a Performance Fee is due to the fund manager. This calculation is straightforward and is the aforementioned difference multiplied by the Performance Fee rate. The Performance Fee is _not_ an annualized fee rate.

The calculation of the Performance Fee requires that, at that moment, no Management Fees are due. This will be true as the code structure always invokes the Management Fee calculation and allocation immediately preceding, and in the same transaction, the Performance Fee calculation.

The Performance Fee calculation business logic is fully encapsulated by the Performance Fee contract. This logic can be represented as follows.

&nbsp;

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<img src="https://latex.codecogs.com/svg.latex?HWM_{MP}$=\begin{cases}S_{n},&S_{n}>HWM_{MP-1}\\HWM_{MP-1},&S_{n}\leq{HWM_{MP-1}}\end{cases}"/>

&nbsp;

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<img src="https://latex.codecogs.com/svg.latex?P_{MP}$=\begin{cases}GAV_{s}-HWM_{MP-1},&GAV_{s}>HWM_{MP-1}\\0,&GAV_{s}\leq{HWM_{MP-1}}\end{cases}"/>

&nbsp;

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<img src="https://latex.codecogs.com/svg.latex?PD_{f}=\frac{P_{MP}T_{n}^2f_{p}}{GAV}$"/>

&nbsp;

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<img src="https://latex.codecogs.com/svg.latex?SPF_{e}=\frac{PD_{f}T_{n}}{T_{n}-PD_{f}}$"/>

where,

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<img src="https://latex.codecogs.com/svg.latex?HWM_{MP}$"/> = high-water mark for the Measurement Period

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<img src="https://latex.codecogs.com/svg.latex?S_{n}$"/> = current share price net of Management Fee

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<img src="https://latex.codecogs.com/svg.latex?GAV$"/> = current fund Gross Asset Value

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<img src="https://latex.codecogs.com/svg.latex?GAV_{s}=\frac{GAV}{T_{n}}$"/> = current fund GAV per share

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<img src="https://latex.codecogs.com/svg.latex?P_{MP}$"/> = performance for the Measurement Period

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<img src="https://latex.codecogs.com/svg.latex?PD_{f}$"/> = pre-dilution quantity of shares

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<img src="https://latex.codecogs.com/svg.latex?\Large&space;T_{n}$"/> = current shares outstanding

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<img src="https://latex.codecogs.com/svg.latex?f_{p}$"/> = Performance Fee rate

&nbsp;&nbsp;&nbsp;&nbsp;<img src="https://latex.codecogs.com/svg.latex?SPF_{e}$"/> = number shares to create to compensate Performance Fees earned during the conversion period

&nbsp;

While Performance Fees are only crystalized at the end of each measurement period, there must be a mechanism whereby redeeming investors compensate for _their_ current share of accrued performance fees prior to redemption.

In the case where an investor redeems prior to the current Measurement Period's end and where the current share price exceeds the fund HWM, a Performance Fee is due. The Redemption business logic calculates the accrued Performance Fee for the entire fund at the time of redemption and weights this by the redemption share quantity's proportion to the fund's total share quantity. The resulting percentage is the proportion of the redemption share quantity due as the redeeming investor's Performance Fee payment. This quantity is deducted from the redeeming share quantity and credited to the manager's share account balance. The remaining redeeming share quantity is destroyed as the proportionate individual token assets are transferred out of the fund and the fund's total share quantity and the investor's share quantity is reduced by this net redeeming share quantity.

**Note on Fee Share Allocation**

The intended behavior is for the Manager to immediately redeem fee shares as they are created. This will ensure a fair and precise share allocation. The implemented code that represents the fee calculations above contain smart contract optimizations for state variable storage. By not immediately redeeming fee shares, a negligible deviation to the manager fee payout will arise due to this optimization.

### FeeManager.sol

##### Description

The Fee Manager is a spoke which is initialized and permissioned in the same manner as all other spokes. The Fee Manager registers and administers the execution order of the individual fee contracts.

##### Inherits from

Spoke, DSMath (link)

##### On Construction

Sets the hub of the spoke.

##### Structs

None.

##### Enums

None.

##### Public State Variables

`Fee[] public fees`

An array of type fees storing the defined fees.

`mapping (address => bool) public feeIsRegistered`

A mapping storing the registration status of a fee address.

##### Public Functions

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

### FixedManagementFee.sol

##### Description

The FixedManagementFee contract contains the complete business logic for the creation of fund shares based on assets managed over a specified time period.

##### Inherits from

Fee and DSMath (link)

##### On Construction

No specified behavior.

##### Structs

None.

##### Enums

None.

##### Public State Variables

`uint public PAYMENT_TERM`

An integer defining the measurement period in seconds.

`uint public MANAGEMENT_FEE_RATE`

An integer defining the management fee percentage rate.

`uint public DIVISOR`

An integer defining the standard divisor.

`uint public lastPayoutTime`

An integer defining the block time in UNIX epoch seconds when the previous fee payout was executed.

##### Public Functions

`function feeAmount(address hub) public view returns (uint feeInShares)`

This function calculates and returns the number of shares to be created given the amount of time since the previous fee payment, asset value and the defined management fee rate.

`function updateState(address hub) external`

This function sets `lastPayoutTime` to the current block timestamp.

### FixedPerformanceFee.sol

##### Description

The FixedPerformanceFee contract contains the complete business logic for the creation of fund shares based on fund performance over a specified Measurement Period and relative to the fund-internally-defined HWM.

Inherits from Fee and DSMath (link)

##### On Construction

No specified behavior.

##### Structs

None.

##### Enums

None.

##### Public State Variables

`uint public PERFORMANCE_FEE_RATE`

An integer defining the performance fee percentage rate.

`uint public DIVISOR`

An integer defining the standard divisor.

`uint public highWaterMark`

An integer defining the asset value which must be exceeded at the measurement period's end to facilitate the determination of the performance fee due to the manager.

`uint public lastPayoutTime`

An integer defining the block time in UNIX epoch seconds when the previous fee payout was executed.

##### Public Functions

`function feeAmount(address hub) public view returns (uint feeInShares)`

This function calculates and returns the number of shares to be created given the fund performance since the previous measurement period payment, asset value and the defined performance fee rate.

`function updateState(address hub) external`

This function sets the `highwatermark` and `lastPayoutTime` if applicable.

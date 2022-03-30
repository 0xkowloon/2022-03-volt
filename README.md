# ✨ So you want to sponsor a contest

This `README.md` contains a set of checklists for our contest collaboration.

Your contest will use two repos: 
- **a _contest_ repo** (this one), which is used for scoping your contest and for providing information to contestants (wardens)
- **a _findings_ repo**, where issues are submitted. 

Ultimately, when we launch the contest, this contest repo will be made public and will contain the smart contracts to be reviewed and all the information needed for contest participants. The findings repo will be made public after the contest is over and your team has mitigated the identified issues.

Some of the checklists in this doc are for **C4 (🐺)** and some of them are for **you as the contest sponsor (⭐️)**.

---

# Contest setup

## 🐺 C4: Set up repos
- [x] Create a new private repo named `YYYY-MM-sponsorname` using this repo as a template.
- [ ] Add sponsor to this private repo with 'maintain' level access.
- [ ] Send the sponsor contact the url for this repo to follow the instructions below and add contracts here. 
- [ ] Delete this checklist and wait for sponsor to complete their checklist.

## ⭐️ Sponsor: Provide contest details

Under "SPONSORS ADD INFO HERE" heading below, include the following:

- [ ] Name of each contract and:
  - [ ] source lines of code (excluding blank lines and comments) in each
  - [ ] external contracts called in each
  - [ ] libraries used in each
- [ ] Describe any novel or unique curve logic or mathematical models implemented in the contracts
- [ ] Does the token conform to the ERC-20 standard? In what specific ways does it differ?
- [ ] Describe anything else that adds any special logic that makes your approach unique
- [ ] Identify any areas of specific concern in reviewing the code
- [ ] Add all of the code to this repo that you want reviewed
- [ ] Create a PR to this repo with the above changes.

---

# Contest prep

## 🐺 C4: Contest prep
- [x] Rename this repo to reflect contest date (if applicable)
- [x] Rename contest H1 below
- [x] Add link to report form in contest details below
- [x] Update pot sizes
- [x] Fill in start and end times in contest bullets below.
- [ ] Move any relevant information in "contest scope information" above to the bottom of this readme.
- [ ] Add matching info to the [code423n4.com public contest data here](https://github.com/code-423n4/code423n4.com/blob/main/_data/contests/contests.csv))
- [ ] Delete this checklist.

## ⭐️ Sponsor: Contest prep
- [ ] Make sure your code is thoroughly commented using the [NatSpec format](https://docs.soliditylang.org/en/v0.5.10/natspec-format.html#natspec-format).
- [ ] Modify the bottom of this `README.md` file to describe how your code is supposed to work with links to any relevent documentation and any other criteria/details that the C4 Wardens should keep in mind when reviewing. ([Here's a well-constructed example.](https://github.com/code-423n4/2021-06-gro/blob/main/README.md))
- [ ] Please have final versions of contracts and documentation added/updated in this repo **no less than 8 hours prior to contest start time.**
- [ ] Ensure that you have access to the _findings_ repo where issues will be submitted.
- [ ] Promote the contest on Twitter (optional: tag in relevant protocols, etc.)
- [ ] Share it with your own communities (blog, Discord, Telegram, email newsletters, etc.)
- [ ] Optional: pre-record a high-level overview of your protocol (not just specific smart contract functions). This saves wardens a lot of time wading through documentation.
- [ ] Delete this checklist and all text above the line below when you're ready.

---

# Volt contest details
- $71,250 USDC main award pot
- $3,750 USDC gas optimization award pot
- Join [C4 Discord](https://discord.gg/code4rena) to register
- Submit findings [using the C4 form](https://code4rena.com/contests/2022-03-volt-contest/submit)
- [Read our guidelines for more details](https://docs.code4rena.com/roles/wardens)
- Starts March 31, 2022 00:00 UTC
- Ends April 6, 2022 23:59 UTC

# Contest scoping

Inflation erodes the purchasing power of savers over time. Unpredictable inflation rates make it impossible to plan ahead financially when the value of money itself is constantly in flux. The U.S. Dollar lost 7.5% of its value in the last year alone, the highest inflation rate in 39 years. Volt offers a solution -- a stablecoin that defeats inflation by tracking real world prices, rather than pegging its value to a depreciating fiat currency.

VOLT will start at a price of $1 and adjust over time according to Consumer Price Index (CPI) data. VOLT holders can preserve their wealth over time without the need to actively manage their savings or take on excessive risk. Like MakerDAO’s DAI, VOLT will be backed by a mixture of user-deposited collateral and protocol-controlled assets. Compared to speculating on volatile assets like Bitcoin or “yield farming” with stablecoins, VOLT is a much easier and safer way for holders to preserve their purchasing power.

## Smart Contracts

All the contracts in this section are to be reviewed. Any contracts not in this list are to be ignored.

Smart contract [architecture docs](https://github.com/volt-protocol/volt-protocol-core/blob/develop/README.md).

### ScalingPriceOracle.sol (223 sloc)
VOLT system oracle contract that receives a chainlink price feed and then linearly interpolates that rate over a 28 day period into the VOLT price. Interest is compounded monthly when the rate is updated.

### OraclePassThrough.sol (66 sloc)
Oracle contract that the whole VOLT system points to. Passes all price calls to the Scaling Price Oracle and returns the result. The Scaling Price Oracle can be changed if there is a decision to change how data is interpolated without needing all contracts in the system to be upgraded, only this contract will have to change where it points

### OracleRef.sol (158 sloc)
Stores a reference to an Oracle and defines some utilities around interacting with the referenced oracle. Used in NonCustodialPSM.

### RateLimited.sol (158 sloc)
Abstract contract for putting a rate limit on how fast a contract can perform an action e.g. Minting. Used in MultiRateLimited and Non Custodial PSM.

### MultiRateLimited.sol (356 sloc)
Abstract contract for putting a rate limit on how fast an address can perform an action e.g. Minting or swapping. There are two buffers, one buffer which is each individual addresses's current buffer, and then there is a global buffer which is the buffer that each individual address must respect as well. Contains role `ADD_MINTER_ROLE` which is allowed to add new addresses within predefined rate limits.

### GlobalRateLimitedMinter.sol (66 sloc)
Global contract to handle rate limited minting of VOLT on a global level. Allows whitelisted minters to call in and specify the address to mint VOLT to within that contract's limits. Leverages MultiRateLimited.sol and RateLimited.sol.

### Volt.sol (96 sloc)
Custom implementation of the ERC20 specification. Contains a reference to Core, and the minter role is allowed to mint VOLT.

### Core.sol (32 sloc)
Source of truth for the VOLT Protocol. Stores all roles, role admins, and which roles different users have.

### Permissions.sol (214 sloc)
Access control module for Core. Sets major contract roles. Provides opinionated methods for accessing openzeppelin's AccessControlEnumerable.sol roles.

### CoreRef.sol (233 sloc)
References the source of truth for the VOLT Protocol. Defines modifiers and utilities around interacting with Core.

### TribeRoles.sol (80 sloc)
Contract that stores VOLT DAO ACL Roles. Holds a complete list of all roles which can be held by contracts inside Tribe DAO.
Roles are broken up into 3 categories:
* Major Roles - the most powerful roles in the Tribe DAO which should be carefully managed.
* Admin Roles - roles with management capability over critical functionality. Should only be held by automated or optimistic mechanisms
* Minor Roles - operational roles. May be held or managed by shorter optimistic timelocks or trusted multisigs.

### Timed.sol (78 sloc)
An abstract contract for timed events. Used to allow execution only after the specified duration has passed.

### Deviation.sol (39 sloc)
Library that determines the change in basis points between two numbers. Used to validate Chainlink oracle inputs to the ScalingPriceOracle.

### CompoundPCVDepositBase.sol (62 sloc)
Base class for a Compound PCV Deposit.

### ERC20CompoundPCVDeposit.sol (50 sloc)
ERC20 implementation for a Compound PCV Deposit.

### PCVDeposit.sol (58 sloc)
Abstract contract for withdrawing ERC-20 tokens using a PCV Controller.

### NonCustodialPSM.sol (461 sloc)
The Non Custodial Peg Stability Module (PSM) is a contract which allows exchange of VOLT at the redemption price for the underlying assets with a fee. All PSM pairs will be VOLT and a Stablecoin.
 * `mint()` - buy VOLT with a stablecoin at current redemption price plus a fee
 * `redeem()` - sell VOLT back to the protocol for the current redemption price

The Non Custodial Peg Stability Module is forked off of TRIBE DAO's implementation of the Peg Stability module. The main differences in VOLT's PSM is that it is non custodial, meaning it does not custody any PCV itself, rather, it atomically deposits all proceeds from selling VOLT into the FEI Fuse PCV Deposit. This allows the VOLT protocol to continually accrue interest from PCV and have no opportunity cost of capital sitting idly in the Peg Stability Module. Deposits on the PCVDeposit are not automatically triggered to save gas. Minting cTokens in Compound is very expensive, so the protocol will foot this cost by calling deposit on the PCVDeposit on a regular basis to ensure capital efficiency.

The Non Custodial Peg Stability Module allows minting through the Global Rate Limited Minter by calling out and minting when it does not have a sufficient VOLT balance. Known issue: the doInvert flag is set to true, which means that all price calculations have a very small loss of precision due to the price inversion.

## Deployment Scripts

### deployCore.sh
Script to deploy the Core and VOLT contracts.

### deployOracle.sh
Script to deploy the ScalingPriceOracle and OraclePassThrough.

## Areas of concern
The Non Custodial Peg Stability Module is one of the centerpieces of this system as it allows users to buy and sell VOLT and is responsible for always keeping the floor price of VOLT at the redemption price. This module is hardcoded to invert the oracle price of VOLT so that the VOLT oracle will work with the Non Custodial PSM. In most deployments of the PSM, all assets that had a non fixed price were the external asset, i.e. one the PSM couldn't mint. So the oracle would fetch the price of the external asset and use that to price swaps. However, in this system, the mintable asset's price is not fixed to a dollar, but determined based off the OraclePassThrough and ScalingPriceOracle, so this system makes the assumption that all external assets i.e. stablecoins maintain their peg.

The oracle system has a small attack surface with only 1 state-changing function that is not permissioned `requestCPIData()`. This function is locked down and only callable once per month after the 14th. The assumption in this system is that there are multiple jobs and parties that want to send a transaction to request this data once per month, so the job will always be triggered within a day of being able to. If the `requestCPIData` function is not called once per month, then the ScalingPriceOracle will not have correct data and there will need to be a governance action to swap out the ScalingPriceOracle that the OraclePassThrough points to.

Any errors in OraclePassThrough are very bad because replacing this contract would require governance actions across multiple DeFi protocols. Any issues that could block updates to the ScalingPriceOracle reference in the OraclePassThrough at the smart contract level are critical. Returning an incorrect price is also critical, however, rounding down by 1 off a price scaled up by 1e18 will not be considered as a valid issue as that implicitly happens.

## Testnet deployment

A working instance of the VOLT Protocol has been deployed to Kovan for this contest. Feel free to poke around, but if you are going to do something that might have a large impact on the contracts, please do so on a fork so that we don't have to redeploy them. All contracts have been verified on Etherscan.

| Mocks                         | Address |
|-------------------------------|------------------------------------------------------|
MockVOLTcToken
MockFEIcToken
VOLT
FEI
	
| Protocol                      | Address |
|-------------------------------|------------------------------------------------------|
NonCustodialPSM
ERC20CompoundPCVDeposit - VOLT
ERC20CompoundPCVDeposit - FEI
GlobalRateLimitedMinter
Core


| Oracle                        | Address |
|-------------------------------|------------------------------------------------------|
OraclePassThrough
ScalingPriceOracle

## Gas Optimizations
The following gas optimizations will not be accepted:
* Making functions payable to save gas.
* Any logic change requiring additional assembly.
* Adding functionality to CompoundPCVDeposit to transfer existing balance before pulling assets out of Compound.
* Make duration in Timed.sol immutable.
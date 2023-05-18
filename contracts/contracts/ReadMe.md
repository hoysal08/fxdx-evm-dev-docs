# `contracts` Folder
This folder contains all smart contracts for Fxdx Exchange EVM platforms.  
This folder contains the following subfolders:

 - [access](#access): contains `Governable` and `TokenManager` contracts
 - [amm](#amm): contains smart contracts to realize `AMM` model
 - [core](#core): contains smart contracts for Trading and Buy/Sell Liquidity
 - [fxdx](#fxdx): contains smart contracts for `FXDX`, `EsFXDX` and `FLP` tokens. Other contracts were used for migration and will not be used later.
 - libraries: contains smart contracts from other libraries
 - [oracle](#oracle): contains smart contracts for price feed oracle
 - [peripherfals](#peripherals): contains smart contracts for timelocks, smart contract state readers, batch sender & updaters
 - [referrals](#referrals): contains `ReferralReader` and `ReferralStorage` contracts to implement referrals.
 - [staking](#staking): contains all contracts for staking and rewarding
 - [tokens](#tokens): contains several contracts for tokens

## access
This folder contains two contracts: `Governable` and `TokenManager`.

`Governable` contract itself isn't deployed. It is used as a super class of the contracts which have governor functionality. This contract has `gov` state, `onlyGov` modifier and `setGov` method.

`TokenManager` contract is a manager contract for tokens and other smart contracts. This contract adds Multisig functionality for token management and contract administration.

## amm
This folder contains several smart contracts for `AMM` model. These contracts were used in the previous protocol versions and are not used in the current platforms.

## core
This folder contains the smart contracts for Trading and Buy/Sell Liquidity.

##### Vault
`Vault` contract is a core contract for trading and all pool tokens are kept here. This contract supports swap, increasePosition, decreasePosition, liquidatePosition, trade fee management and buy / sell USDF (basic functions for liquidity). It provides many methods to get / set states that affects the above core functionalities.  
In the exchange platform, `Vault` trade methods are not directly called in the interface. In the interface, router methods are called and `Vault` methods are called in these router methods.

##### FlpManager
`FlpManager` contract is the core contract for managing liquidity. It supports `addLiquidity` and `removeLiquidity` functionality. When adding liquidity, the `buy` tokens will be sent to `Vault` and `FLP` will be minted. And when removing liquidity, `FLP` will be burnt and `sell` tokens will be sent to the seller account.  
Like in `Vault`, `FlpManager` liquidity methods are not directly called in the interface. In the interface, `rewardRouter` methods are called and `FlpManager` liquidity methods are called in these `rewardRouter` methods.

##### OrderBook
`OrderBook` contract is the core contract for managing limit and stop orders. It supports creating / updating / executing / canceling `IncreasePosition`, `DecreasePosition` and `Swap` limit and stop orders.
`OrderBook` methods are called directly from interface and order keeper.

##### Router
`Router` contract is the core contract for routing trader requests to vault core methods. It also supports `plugin transfer`. All contracts that are added as plugins and get user approval can transfer approved user tokens from the user account to other accounts. By using this `plugin` functionality, users can approve tokens only for `Router` contract, and plugin contracts can transfer approved user tokens according to their logic.

##### BasePositionManager
`BasePositionManager` contract itself isn't deployed. It is used as a super contract of `PositionManager` and `PositionRouter` contracts. It provides fundamental states and methods that are used in both `PositionManager` and `PositionRouter` contracts. It supports deposit fee manangement, increase / decrease position, swap, and global long / short limit management.

##### PositionMananger
`PositionManager` is the core contract that routes requests from `liquidator` to `router` and `vault`, and order execution requests from `order keeper` to `orderBook`. Besides them, it supports several other small functionalities.

##### PositionRouter
`PositionRouter` is the core contract that supports creating / executing / canceling increase / decrease position requests from traders. `Increase / Decrease Position` request creation methods are called directly from the interface. Execution and cancel methods can be called directly from traders but it's not preferred.
Bulk execution and cancel methods can only be called from `positionKeeper`, which can be a normal user account or `fastPriceFeed` contract.  
Almost all position requests are handled by these bulk execution / cancel methods.  
The main purpose of `PositionRouter` is to make sure position `increase` and `decrease` actions executed at the instant price, and prevent front runners from getting benefits from price submission interval. `Fast / Secondary` prices are submitted in a certain interval and if you use only these prices, front runners can exploit the price difference between adjacent price submissions.  
In order to prevent this, prices are submitted first and then position requests are executed / canceled based on them.

##### SwapRouter
`SwapRouter` is the core contract that supports creating / executing / canceling swap requests from traders. Swap request creation methods are called directly from the interface. Execution and cancel methods can be called directly from traders but it's not preferred.
Bulk execution and cancel methods can only be called from `requestKeeper`, which can be a normal user account or `fastPriceFeed` contract.  
Almost all swap requests are handled by these bulk execution / cancel methods.  
The main purpose of `SwapRouter` is to make sure swap actions executed at the instant price, and prevent front runners from getting benefits from price submission interval. `Fast / Secondary` prices are submitted in a certain interval and if you use only these prices, front runners can exploit the price difference between adjacent price submissions.  
In order to prevent this, prices are submitted first and then swap requests are executed / canceled based on them.

##### LiquidityRouter
`LiquidityRouter` is the core contract that supports creating / executing / canceling liquidity requests from investors. `Add / Remove Liquidity` request creation methods are called directly from the interface. Execution and cancel methods can be called directly from investors but it's not preferred.
Bulk execution and cancel methods can only be called from `requestKeeper`, which can be a normal user account or `fastPriceFeed` contract.  
Almost all liquidity requests are handled by these bulk execution / cancel methods.  
The main purpose of `LiquidityRouter` is to make sure add / remove liquidity actions executed at the instant price, and prevent front runners from getting benefits from price submission interval. `Fast / Secondary` prices are submitted in a certain interval and if you use only these prices, front runners can exploit the price difference between adjacent price submissions.  
In order to prevent this, prices are submitted first and then add / remove liquidity requests are executed / canceled based on them.

##### VaultPriceFeed
`VaultPriceFeed` is the core contract to manage various factors that affects the prices which will be used for trade and liquidity management. It also supports getter functions to get the prices from Primary and Secondary price feed data.

##### VautlErrorController
`VaultErrorController` is a simple contract for setting error codes for `Vault` contract `require` failures.

##### VaultUtils
`VaultUtils` is a contract that contains utility functions that are used in `Vault` contract.

## fxdx
This folder contains smart contracts for `FXDX`, `EsFXDX`, and `FLP` tokens. Other contracts were used for migration and will not be used later.

`FXDX` is the utility token that is used for platform governance. Users can directly get FXDX tokens from other external swaps or get them by vesting EsFXDX tokens got as rewards. Users can stake `FXDX` tokens to get rewards and attend the platform governance.

`FLP` is the liquidity token that are provided to the users when they buy liquidity.

`EsFXDX` is the escrowed FXDX token that are distributed as rewards to users who bought liquidity and staked tokens.  
`EsFXDX` can be vested to get `FXDX` or can be staked to get rewards.

## oracle
This folder contains smart contracts for price feeds: `PriceFeed`, `FastPriceFeed` and `FastPriceEvents`.

`PriceFeed` is a contract that mimics the `Chainlink` price feed contract. It is not deployed and it is just used as a reference.

`FastPriceFeed` is an oracle contract to submit prices from several reliable price feeds to the chain. By using this contract, you can get the reliable prices by combining the prices from several sources. `FastPriceFeed` also execute position requests as a position keeper.

`FastPriceEvents` emits `UpdatePrice` events whenever new prices are submitted to `FastPriceFeed`.

## peripherals
This folder contains smart contracts for timelocks, smart contract state readers, batch sender & updaters.

`BalanceUpdate` is a contract to update `vault` token balances.

`BatchSender` is a contract to send tokens to several accounts at a time.

`EsFxdxBatchSender` is contract to send `EsFXDX` token to several accounts at a time.

`FxdxTimelock` is a contract that will be used as a governor for other contracts. It makes sure that important state updates in contracts are delayed by `buffer` after being signaled. This way, traders and investors can be notified in advance before the platform state is actually updated. `FxdxTimelock` is under development and it is not used yet. `Timelock` and `PriceFeedTimelock` are being used.

`Timelock` is a timelock contract that will be used as a governor for other contracts. It is used as a governor for `Vault`, `OrderBook`, `FlpManager` and contracts related to staking and rewarding.

`PriceFeedTimelock` is a timelock contract that will be used as a governor for price oracle contracts: `FastPriceFeed` and `VaultPriceFeed`.

`Reader`, `VaultReader`, `OrderBookReader`, `RewardReader` are the contracts that make it easy to collect necessary information from the deployed platform smart contracts efficiently with the least amount of requests.

## referrals
This folder contains `ReferralReader` and `ReferralStorage` contracts to implement referrals.

`ReferralStorage` is a contract that manages referral code owners and tiers. This information will be used to provide rebates to the referred users and this will have a great impact on increasing the platform users.

`ReferralReader` is a contract that provides a method to get code owner accounts corresponding to provided codes list. It collects the data from `ReferralStorage` contract.

## staking
This folder contains all contracts for staking and rewarding.

##### BonusDistributor
`BonusDistributor` is a contract that distributes `Multiplier Points` rewards to `FXDX + esFXDX` stakeholders. It distributes rewards evenly over time according to `bonusMultiplierBasisPoints` state.
`BonusDistributor` methods are not called directly from the interface but they are called in `RewardTracker` methods.

##### RewardDistributor
`RewardDistributor` is a contract that distributes token rewards to stakeholders. It provides `esFXDX` token rewards to `FXDX + esFXDX` stakeholders and `FLP` holders, and provides `nativeToken` rewards to `FXDX + esFXDX + MultiplierPoints` stakeholders and `FLP` holders. It distributes token rewards evenly over time according to `tokensPerInterval` state.  
4 `RewardDistributor` contracts are deployed per staked token and per reward token (see [Deployment Procedure](../DeploymentProcedure.md) for more information).  
`RewardDistributor` methods are not called directly from the interface but they are called in `RewardTracker` methods.

##### RewardTracker
`RewardTracker` is a contract that supports stake / unstake of tokens and tracks rewards based on staked token amount for stakeholders. It manages all userwise stake and reward information. It provides the methods for staking, unstaking and claiming rewards.  
5 `RewardTracker` contracts are deployed per staked token and per reward token (see [Deployment Procedure](../DeploymentProcedure.md) for more information).  
`RewardTracker` methods are not called directly from the interface but they are called in `RewardRouter` methods.

##### Vester
`Vester` is a contract that lets users vest `esFXDX` tokens to `FXDX` tokens. Vested `esFXDX` tokens will be burnt and the same amount of `FXDX` tokens will be provided evenly over a year. Vested `esFXDX` tokens can't be staked.  
When vesting is initiated, the average amount of `FXDX` or `FLP` tokens that was used to earn the `esFXDX` rewards will be reserved.  
`esFXDX` tokens that have been unstaked and deposited for vesting will not earn rewards. Staked tokens that are reserved for vesting will continue to earn rewards.
After initiating vesting, the `esFXDX` tokens will be converted into `FXDX` every second and will fully vest over 365 days. `esFXDX` tokens that have been converted into GMX are claimable at anytime.

Tokens that are reserved for vesting cannot be unstaked or sold. To unreserve the tokens, you can call `withdraw` method. Partial withdrawals are not supported, so withdrawing will withdraw and unreserve all tokens as well as pause vesting. All `esFXDX` tokens that had been vested into `FXDX` will remain as `FXDX` tokens.

`Vester` contract provides states and methods to realized the above functionalities.  
2 `Vester` contracts are deployed according to reserved tokens: `fxdxVester` and `flpVester` (see [Deployment Procedure](../DeploymentProcedure.md) for more information).  
`Vester` methods are not called directly from the interface but they are called in `RewardRouter` methods.

##### RewardRouter
`RewardRouter` is a contract that routes user requests to `RewardTracker`s and `Vester`s. It provides many methods for staking / unstaking various tokens, minting and staking Flp tokens, unstaking and redeaming `FLP` tokens, claiming and compounding rewards. It also keep tracks of all deployed smart contracts for staking and rewarding.  
`RewardRouter` methods are called directly from the interface and call `FlpManager`, `RewardTracker` and `Vester` methods internally.  
There are two versions of reward router: `RewardRouter` and `RewardRouterV2`. `RewardRouter` is a legacy contract and will not be deployed for new platforms. `RewardRouterV2` will be used.

##### FlpBalance
`FlpBalance` is a contract to manage user `FLP` balance.

##### StakedFlp
`StakedFlp` is a contract to manage user `FLP` balance. `StakedFlp` methods are used when `FLP` holders transfer ownership to other account.

##### StakeManager
`StakeManager` provides a method `stakeForAccount`. This method will be used when the governor stakes tokens for another user.

## tokens
This folder contains several contracts for tokens.

##### BaseToken
`BaseToken` is a contract that defines an ERC20 token that has `yield` functionality. This token is not deployed. This contract will be used as a super contract of `MintableBaseToken`.

##### MintableBaseToken
`MintableBaseToken` is a contract that defines an ERC20 token that has `yield` functionality and `minter` management. If an account is set as a minter, it can mint and burn the tokens.  
`MintableBaseToken` is a super contract of `FXDX`, `EsFXDX`, `FLP`, and `SnapshotToken`, and `bnFXDX` (Bonus FXDX) token is deployed by using this contract.

##### SnapshotToken
`SnapshotToken` is a child contract of `MintableBaseToken` that supports `batchMint` functionality. It is a legacy contract and it is not used in the current platforms.

##### FaucetToken
`FaucetToken` is an `ERC20` token that provides `faucet` functionality. Besides methods from `ERC20`, it has states and methods for `faucet`. This token is not deployed on MainNet. It is used to deploy TestNet platform tokens. TestNet platform users can get the deployed test tokens by calling `faucet` methods.

##### Token
`Token` is an `ERC20` token implementation. It is not deployed but it will be used as a super contract of `ERC20` tokens in some scripts.

##### YieldToken
`YieldToken` is an `ERC20` token that has `yield` functionality. This contract is not deployed, and it is used as a super contract of `USDF` and `YieldFarm`.

##### YieldFarm
`YieldFarm` is a `YieldToken` that lets users stake other tokens in order to mint the tokens. When users unstake, tokens are burnt and the same amount of stakedTokens are sent to them. `YieldFarm` is a legacy contract and it is not used in the current platforms.

##### YieldTracker
`YieldTracker` is a tracker contract for staked `YieldToken`s and rewards. It tracks staked token amount per user and distribute rewards for the users. This is a legacy contract and is not used in the current platforms.

##### TimeDistributor
`TimeDistributor` is a contract that distributes rewards for `YieldToken` stakeholders. This is a legacy contract and is not used in the current platforms.

##### USDF
`USDF` is a `YieldToken` that has `vault` management functionality. `vault` is a minter. `USDF` is an internal token to manage liquidity and is not used externally. Deployed `FlpManager` contract is set as a `vault` of the deployed `USDF` token.  
It has the price 1 USD. `USDF` tokens are minted when users buy liquidity and burnt when users sell liquidity.

##### WETH
`WETH` is an `ERC20` token used as a wrapper for the chain native tokens. Besides normal ERC20 methods, it has `deposit` and `withdraw` methods to support wrapping native tokens to wrapped tokens and unwrapping wrapped tokens to native tokens.
Minting and burning of `WETH` tokens are done only by `deposit` and `withdraw` methods.  
`WETH` tokens has the exactly same price as native tokens and no additional fees are applied when wrapping / unwrapping.  
`WETH` tokens are deployed and used in order to make the whole platform calculations simple. By using `WETH`, you can handle native tokens as `ERC20` tokens after wrapping, and this makes the whole process simplified.

##### Bridge
`Bridge` is a contract to support wrapping and unwrapping two tokens. It is a legacy contract and is not used in the current platforms.
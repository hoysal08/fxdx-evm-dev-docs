# Subgraphs
Repository: [https://github.com/fxdxdex/fxdx-evm-subgraph](https://github.com/fxdxdex/fxdx-evm-subgraph)

This repository contains subgraphs for Fxdx Exchange on EVM-compatible blockchains.  
It is based on Node and it requires Node version 16.  
Subraphs are deployed to TheGraph Hosted Service.

## Structure
Each exchange platform should have 5 subgraphs in order to work properly:

- [actions](#actions): manages user actions (trade history) used to display contents in `Trades` section and `Actions` page of web interface
- [admin](#admin): manages admin user pending actions used to update important states in admin web app portal.
- [orders](#orders): manages all limit orders in the platform and is used in order executor in order to get the active orders.
- [positions](#positions): manages all positions in the platform and is used in liquidator and order executor in order to get the active positions.
- [prices](#prices): manages price candles for all unstable tokens and periods and is used in stats API in order to return price candles for the specified token, period and chain.
- [referrals](#referrals): manages referrals data used in stats and interface web app.
- [stats](#stats): manages all stats data, including volume, fees, pool composition, etc., consumed in all other platform projects.

Each subgraph has the following main folders and files:

- `abis` folder contains ABI files of the smart contracts from which events are collected
- `src` folder contains files with the event handler functions that maps smart contract event to subgraph `entity`.
- `schema.graphql` file contains subgraph `entity` definitions. These are the data models and smart contract events data is collected in the form of these `entities`
- `subgraph.yaml` file defines `specVersion`, `description`, `repository`, `schema` and `dataSources` for the subgraph. `dataSources` is most important. It defines the chain name, smart contract address, ABI file, start blocks, event handlers and the mapper files.

## actions
This subgraph manages user actions (trade history) used to display contents in `Trades` section and `Actions` page of web interface.

The folder and subgraph names are defined as `fxdx-<network>-actions` for consistency, where `<network>` is the chain name such as `goerli`, `moonbeam`, `arbitrum`, etc.

This subgraph has only one `entity` named `Action`. All user actions are kept in the form of `Action`.

It collects events from `OrderBook` (create, cancel, update and execute order actions), `Vault` (swap, increase / decrease / liquidate position actions), `PositionRouter` (increase / decrease position requests create and cancel actions), `SwapRouter` (create / exectue / cancel swap requests), `LiquidityRouter` (createAddLiquidity, cancelAddLiquidity, createRemoveLiquidity and cancelRemoveLiquidity actions), and `FlpManager` (AddLiquidity and RemoveLiquidity actions) contract events.

## admin
This subgraph manages admin user pending actions used to update important states in admin web app portal.

The folder and subgraph names are defined as `fxdx-<network>-admin` for consistency, where `<network>` is the chain name such as `goerli`, `moonbeam`, `arbitrum`, etc.

This subgraph has two entities: `TimelockAction` and `TokenManagerAction`. All timelock and priceFeedTimelock pending actions are kept in the form of `TimelockAction` and all tokenManager multisig actions are kept in the form of `TokenManagerAction`.

It collects events from `Timelock` (signal approve / withdrawToken / mint / setGov / setHandler / setPriceFeed / redeemUsdf / vaultSetTokenConfig, and clearAction events), `PriceFeedTimelock` (signal approve / withdrawToken / setGov / setPriceFeedWatcher / setPriceFeedUpdater / priceFeedSetTokenConfig and clearAction events), `TokenManager` (signal approve / approveNFT / approveNFTs / setAdmin / setGovm, signAction and clearAction events) contract events.

## orders
This subgraph manages all limit orders in the platform and it is used in order executor in order to get the active orders.

The folder and subgraph names are defined as `fxdx-<network>-orders` for consistency, where `<network>` is the chain name such as `goerli`, `moonbeam`, `arbitrum`, etc.

This subgraph has 3 entities: `IncreaseOrder`, `DecreaseOrder` and `SwapOrder`. IncreasePosition, DecreasePosition, and Swap orders are kept in the form of `IncreaseOrder`, `DecreaseOrder`, and `SwapOrder` respectively.

It collects position data from `Vault` contract. It uses `IncreasePosition`, `UpdatePosition`, `ClosePosition`, and `LiquidatePosition` events in order to create and update `Position` entities.

## positions
This subgraph manages all positions in the platform and it is used in liquidator and order executor in order to get the active positions.

The folder and subgraph names are defined as `fxdx-<network>-positions` for consistency, where `<network>` is the chain name such as `goerli`, `moonbeam`, `arbitrum`, etc.

This subgraph has only one `entity` named `Position`. All trader positions are kept in the form of `Position`.

It collects position data from `OrderBook` contract. It uses `CreateIncreaseOrder`, `CancelIncreaseOrder`, `ExecuteIncreaseOrder`, `UpdateIncreaseOrder`, `CreateDecreaseOrder`, `CancelDecreaseOrder`, `ExecuteDecreaseOrder`, `UpdateDecreaseOrder`, `CreateSwapOrder`, `CancelSwapOrder`, `ExecuteSwapOrder`, and `UpdateSwapOrder` events in order to create and update `IncreaseOrder`, `DecreaseOrder` and `SwapOrder` entities.

## prices
This subgraph manages price candles for all unstable tokens and periods, and it is used in stats API in order to return price candles for the specified token, period and chain.

The folder and subgraph names are defined as `fxdx-<network>-prices` for consistency, where `<network>` is the chain name such as `goerli`, `moonbeam`, `arbitrum`, etc.

This subgraph has only one `entity` named `PriceCandle`. All token prices are kept in the form of `PriceCancle`. These candles are used to draw price charts in the main interface project.

It collects position data from `FastPriceEvents` contract. It uses `PriceUpdate` events in order to create and update `PriceCandle` entities.

## referrals
This subgraph manages referrals data used in stats and interface web app.

The folder and subgraph names are defined as `fxdx-<network>-referrals` for consistency, where `<network>` is the chain name such as `goerli`, `moonbeam`, `arbitrum`, etc.

This subgraph defines several `entities` in order to keep referrals data efficiently for the later use: `Tier`, `Referrer`, `Distribution`, `ReferralVolumeRecord`, `ReferrerStat`, `ReferralCode`, `TradedReferral`, `ReferralStat`, `GlobalStat`, `ExecuteDecreaseOrder`, and `PositionReferralAcrion`. These entities are defined according to the demands in stats and interface web apps.

It collects referrals data from `OrderBook` (ExecuteIncreaseOrder event), `BatchSender` (BatchSend event), `PositionManager` (IncreasePositionReferral and DecreasePositionReferral events), `PositionRouter` (IncreasePositionReferral and DecreasePositionReferral events), `ReferralStorage` (GovSetCodeOwner, RegisterCode, SetCodeOwner, SetReferrerDiscountShare, SetReferrerTier, SetTier, and SetTraderReferralCode events) contract events.

## stats
This subgraph manages all stats data, including volume, fees, pool composition, etc., consumed in all other platform projects.

The folder and subgraph names are defined as `fxdx-<network>-stats` for consistency, where `<network>` is the chain name such as `goerli`, `moonbeam`, `arbitrum`, etc.

This subgraph defines several `entities` in order to keep stats data efficiently for the later use: `WhitelistedToken`, `TokenStat`, `ActivePosition`, `LiquidationType`, `LiquidatedPosition`, `ChainlinkPrice`, `UniswapPrice`, `FastPrice`, `FundingRate`, `TradingStat`, `Order`, `OrderStat`, `UserData`, `UserStat`, `HourlyFlpStat`, `FlpStat`, `Swap`, `HourlyVolume`, `VolumeStat`, `HourlyVolumeBySource`, `HourlyVolumeByToken`, `HourlyFee`, `FeeStat`, `Transaction`, and `FxdxStat`. These entities are defined according to the demands in stats and interface web apps.

It collects stats data from `ChainlinkAggregator` (AnswerUpdated event), `FlpManager` (AddLiquidity and RemoveLiquidity events), `OrderBook` (CreateIncreaseOrder, CancelIncreaseOrder, ExecuteIncreaseOrder, CreateDecreaseOrder, CancelDecreaseOrder, ExecuteDecreaseOrder, CreateSwapOrder, CancelSwapOrder, and ExecuteSwapOrder events), `FastPriceEvents` (PriceUpdate event), `UniswapPool` (Swap event), `RewardDistributor` (`FeeFxdxRewardDistributor` -> Distribute event, `EsfxdxFxdxRewardDistributor` -> Distribute event, `FeeFlpRewardDistributor` -> Distribute event, and `EsfxdxFlpRewardDistributor` -> Distribute event), and `Vault` (UpdateFundingRate, Swap, CollectMarginFees, BuyUSDF, SellUSDF, IncreasePosition, UpdatePosition, ClosePosition, DecreasePosition, LiquidatePosition, IncreasePoolAmount, DecreasePoolAmount, IncreaseReservedAmount, DecreaseReservedAmount, IncreaseUsdfAmount, and DecreaseUsdfAmount events) contract events.

## Production Deployment
1. create 5 subgraphs on [TheGraph Hosted Service Dashboard](https://thegraph.com/hosted-service/dashboard): `Fxdx <Network> Actions`, `Fxdx <Network> Positions`, `Fxdx <Network> Prices`, `Fxdx <Network> Referrals`, and `Fxdx <Network> Stats`, where `<Network>` will be name of the chain on which the exchange platform works, like Goerli, MoonBeam, Arbitrum, etc.

2. duplicate `fxdx-moonbeam-actions`, `fxdx-moonbeam-positions`, `fxdx-moonbeam-prices`, `fxdx-moonbeam-referrals`, and `fxdx-moonbeam-stats` folders in the repository and update the duplicated folder names to `fxdx-<network>-actions`, `fxdx-<network>-positions`, `fxdx-<network>-prices`, `fxdx-<network>-referrals`, and `fxdx-<network>-stats`, where `<network>` is the lowercased chain name like `goerli`, `arbitrum`, `avalanche`, etc.

3. create `.access_token` file in each folder. copy `access_token` from TheGraph account and paste it to the newly created files.

4. update `package.json` file in the newly created folders. update the subgraph name part of `scripts -> deploy` script to the newly created subgraph names.

5. update `subgraph.yaml` file in newly created folders. only `dataSources` part is needed to be updated.

- update `network` field of each data source to the lowercased name of the chain on which the exchange platform works.

- update `source -> address` field of each data source to the corresponding smart contract address in the new chain.

- update `source -> startBlock` field of each data source to the corresponding smart contract deployment transaction block in the new chain.

- in `fxdx-<network>-stats` subgraph, add / remove `ChainlinkAggregator` sources according to the unstable token list used in the new chain. you can get the smart contract addresses from [Chainlink Price Feed Contract Addresses](https://docs.chain.link/data-feeds/price-feeds/addresses).

6. update `src -> helper.ts` file in `fxdx-<network>-stats` folder.  
update token addresses, `getTokenDecimals` and `getTokenPrice` functions according to the token list used in the new exchange platform.  
Token addresses here should be all lowercases here.

7. in order to build the sugraph and deploy it to TheGraph Hosted Service, run the following commands in each new folder:  

- generate types from `schema.graphql` and `subgraph.yaml`
```
yarn codegen
```
- build the subgraph
```
yarn build
```
- deploy the subgraph
```
yarn deploy
```

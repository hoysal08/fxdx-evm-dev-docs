# Deployment Procedure
Deploying all smart contracts and preparing them for exchange are very complicated process.

You should deploy all smart contracts and update their states according to needs.

Many smart contracts depends on some other smart contracts deployed before, and updating states of one smart contract can affect the behavior of other smart contracts.
So you should be very careful when deploying smart contracts and updating their states.

## Before Running Scripts
You should do the followings before running deployment scripts:

#### Update `env.json`
add `<NETWORK>_URL` and `<NETWORK>_DEPLOY_KEY` to `env.json` file.  
For example, you can add `MOONBEAM_URL` and `MOONBEAM_DEPLOY_KEY` for MoonBeam chain.

`<NETWORK>_URL` is a RPC url for the chain and `<NETWORK>_DEPLOY_KEY` is the private key of the deployer account.  
When deploying smart contracts, this deployer account is set as a `gov` or `admin` account. You can change the `gov` and `admin` of the deployed smart contracts later.

#### Update `hardhat.config.js`
add `<network>` setting to `networks` section of `hardhat.config.js`. This setting should include `url`, `chainId`, and `accounts`. For example, in order to deploy smart contracts to MoonBeam chain, you can add:
```
moonbeam: {
    url: MOONBEAM_URL,
    chainId: 1284,
    accounts: [MOONBEAM_DEPLOY_KEY]
}
```
where `MOONBEAM_URL` and `MOONBEAM_DEPLOY_KEY` are from `env.json`.

#### Update `scripts/core/tokens.js`
add `<network>` object to `module.exports`. For MoonBeam chain, you can add `moonbeam: {}` object.  
Then add the tokens to be used in exchange and relevant information to `<network>` object. You can see other network object and set the similar values for the same token. You can update the values after deploying smart contracts according to needs.  
For `nativeToken` object, you can add placeholder values first and then replace them with actual values after deploying `WETH` contract.  
For token object `priceFeed` addresses, you can get them from `Chainlink` [Price Feed Contract Addresses](https://docs.chain.link/data-feeds/price-feeds/addresses).

#### Update `scripts/core/addresses.js`
add `<network>` object to `module.exports`. You can add `moonbeam: {}` object for MoonBeam chain.  
For simplicity, you can copy and pase `goerli` object, replace key with `<network>` and replace address values with emptry string.  
You can set `admin` value to the deployer account address. Then you can determine `priceSender`, `positionsKeeper`, `orderKeeper`, `liquidator`, and `mintReceiver` accounts and set the corresponding address values.  
`signers` will be used for Multisig features in `TokenManager`, `Timelock` and `PriceFeedTimelock` contracts. You can determine who will be the signer and set their account addresses. You can change them later.  
For other keys like `vault`, `router`, `usdf`, etc., you will set their values while deploying the corresponding smart contracts.

## Running Deployment Scripts
After following the above guide, you can run the scripts in the following order.  
You can run the scripts by running the following command:
```
npx hardhat run --network <network> path/to/the/script
```
For example, in order to run `scripts/access/deployTokenManager.js` against MoonBeam chain, you can run the following command:
```
npx hardhat run --network moonbeam scripts/access/deployTokenManager.js
```
In the following description, `addresses` object means `<network>` object of `scripts/core/addresses.js` exported object. For example, for MoonBeam chain, it means `moonbeam` object of `scripts/core/addresses.js` exported object.

##### scripts/access/deployTokenManager.js
This script will deploy `TokenManager` contract. After deployment, set `tokenManager` value of `addresses` object to the deployed smart contract address.

##### scripts/tokens/deployTokens.js
This script will deploy tokens that will be used in the exchange. For TestNet platforms, you can deploy all tokens that will be used in the platform. For MainNet platform, you only need to deploy WETH contract.  
After running the scripts, fill in the corresponding values in `scripts/core/tokens.js` with the deployed token / contract addresses. Set `nativeToken` address of `scripts/core/tokens.js` object with the deployed `WETH` token address.

##### scripts/tokens/mint.js
This script is only for TestNet.
Update the token list and run the script. Then you can get the specified amount of TestNet tokens you deployed by running the above script. By default, the minted tokens will be sent to the deployer account.

##### scripts/core/deployVault.js
This script will deploy the contracts needed for core Trade, including `Vault`, `USDF`, `Router`, `VaultPriceFeed`, `FLP`, `FlpManager`, `VaultErrorController` and `VaultUtils`, and initialize them. You can change the parameters according to your need.  
After running the script, set `vault`, `usdf`, `router`, `vaultPriceFeed`, `flp`, `flpManager`, `vaultErrorController`, and `vaultUtils` fields of `addresses` object to the deployed smart contracts addresses.

##### scripts/core/deployOrderBook.js
This script will deploy `OrderBook` contract and initialized it. Before running the script, set `minExecutionFee` to the value appropriate for the selected chain. After deployment, fill `orderBook` field of `addresses` object with the deployed contract address.

##### scripts/core/addPluginOrderBook.js
add `orderBook` contract deployed in the above script as a plungin to `router` contract.

##### scripts/core/deploySwapRouter.js
This script will deploy `SwapRouter` contract and initialize it. Before running the script, set `minExecutionFee` value to the one appropriate to the selected chain. It can be updated later. After deployment, fill `swapRouter` of `addresses` object with the deployed smart contract address.

##### scripts/core/deployPositionManager.js (comment out parts related to timelock and run them after deploying timelock)
This script will be executed twice. At this stage of deployment, comment out the parts related to timelock and execute the script. The parts commented out will be executed after deploying `timelock`. After deployment, fill `positionManager` of `addresses` object with the deployed smart contract address.

##### scripts/core/deployPositionRouter.js (comment out parts related to timelock and run them after deploying timelock)
This script will be executed twice. At this stage of deployment, comment out the parts related to timelock and execute the script. The parts commented out will be executed after deploying `timelock`. Before running the script, set `minExecutionFee` value to the one appropriate to the selected chain. It can be updated later. After deployment, fill `positionRouter` of `addresses` object with the deployed smart contract address.

##### scripts/core/deployReferralReader.js
This script will deploy `ReferralReader` contract. After deployment, fill `referralReader` of `addresses` object with the deployed smart contract address.

##### scripts/core/deployReferralStorage.js
This script will deploy `ReferralStorage` contract and initialize it. After deployment, fill `referralStorage` of `addresses` object with the deployed smart contract address.

##### scripts/core/setTiersReferralStorage.js
This script will set tiers and referrerTiers for `referralStorage` contract.

##### scripts/fxdx/deployFXDX.js
This script will deploy `FXDX` contract (ERC20 token). After deployment, fill `fxdx` field of `addresses` object with the deployed smart contract address.

##### scripts/fxdx/deployTokens.js
This script will deploy `EsFXDX` contract. After deployment, fill `esFxdx` field of `addresses` object with the deployed smart contract address.

##### scripts/staking/deployRewardRouterV2.js
This script will deploy the contracts related to staking and rewarding, and initialize them. After deployment fill the following fields of `addresses` object with the deployed smart contract addresses in order:
 - stakedFxdxTracker
 - stakedFxdxDistributor
 - bonusFxdxTracker
 - bonusFxdxDistributor
 - feeFxdxTracker
 - feeFxdxDistributor
 - feeFlpTracker
 - feeFlpDistributor
 - stakedFlpTracker
 - stakedFlpDistributor
 - fxdxVester
 - flpVester
 - rewardRouterV2

##### scripts/core/deployLiquidityRouter.js
This script will deploy `LiquidityRouter` contract and initialize it. Before running the script, set `minExecutionFee` value to the one appropriate to the selected chain. It can be updated later. After deployment, fill `liquidityRouter` of `addresses` object with the deployed smart contract address.

##### scripts/peripherals/deployTimelock.js
This script will deploy `Timelock` contract and initialize it. It will also set `gov` address of `vault` contract to the deployed `timelock` address. Before running the script, set `buffer` value to the proper one. You'd better set it to 0, and then set it to the appropriate value after finishing the whole deployment. If not, the whole deployment process will be delayed by at least `buffer` seconds. After deployment, fill `timelock` of `addresses` object with the deployed smart contract address.

##### scripts/peripherals/deployPriceFeedTimelock.js
This script will deploy `PriceFeedTimelock` contract and initialize it. Before running the script, set `buffer` value to the proper one. You'd better set it to 0, and then set it to the appropriate value after finishing the whole deployment. If not, the whole deployment process will be delayed by at least `buffer` seconds. After deployment, fill `priceFeedTimelock` of `addresses` object with the deployed smart contract address.

##### scripts/core/deployPriceFeed.js
This script will deploy `FastPriceFeed` contract and initialize it. Before running the script, get token list from `scripts/core/tokens` object and extract `fastPriceTokens`. `fastPriceTokens` are the ones which have `fastPricePrecision` value. They are unstable and shortable. This script will deploy two contracts: `FastPriceEvents` and `FastPriceFeed`. After deployment, fill `fastPriceEvents` and `fastPriceFeed` fields of `addresses` object with the deployed smart contract addresses.

##### scripts/core/deployPositionManager.js (for timelock setting)
At this stage, just run the parts related to `timelock`. You should comment out the parts that were run at the first stage and uncomment the parts related to `timelock`. You should comment out the line for `PositionManager` deployment and uncomment the one just below it. After running the script, revert the scripts changes back for the later use for other platform deployment.

##### scripts/core/deployPositionRouter.js (for timelock setting)
At this stage, just run the parts related to `timelock`. You should comment out the parts that were run at the first stage and uncomment the parts related to `timelock`. You should comment out the line for `PositionRouter` deployment and uncomment the one just below it. After running the script, revert the scripts changes back for the later use for other platform deployment.

##### scripts/core/setFees.js
This script will update fee-related states of `vault` contract.

##### scripts/core/setMaxGlobalSizes.js
This script will set `maxGlobalLongSize` and `maxGlobalShortSize` of whitelisted tokens. First get the token list from `scripts/core/tokens.js` and update the tokens part of the script. Then run the script.

##### scripts/core/priceFeedSetTokenConfig.js
This script will update the states related to token price feed settings in `vaultPriceFeed` contract. First get the token list from `scripts/core/tokens.js` and update the tokens part of the script.  
You should run this script twice with different method names because `timelock` is the `gov` of `vaultPriceFeed`. First run `signalPriceFeedSetTokenConfig` method and then `priceFeedSetTokenConfig`.

##### scripts/core/vaultSetTokenConfig.js
This script will update the states related to token trade settings in `vault` contract. First get the token list from `scripts/core/tokens.js` and update the tokens part of the script.  
You should run this script twice with different method names because `timelock` is the `gov` of `vault`. First run `signalVaultSetTokenConfig` method and then `vaultSetTokenConfig`.

##### scripts/core/setPrices.js
This script will set the token prices in `fastPriceFeed`. This script has two purposes: one is to check if the price submission works as expected and the other is to set prices of tokens for the first time.
Before running the script, update `<NETWORK>_DEPLOY_KEY` to the private key of `priceSender` account.  
Update tokens list and prices list in the script and run it. After running the script, revert `<NETWORK>_DEPLOY_KEY` value to the deployer account private key.

##### scripts/peripherals/deployBalanceUpdater.js
This script will deploy `BalanceUpdater` contract. After deployment, fill `balanceUpdater` field of `addresses` object with the deployed smart contract address.

##### scripts/peripherals/deployBatchSender.js
This script will deploy `BatchSender` contract. After deployment, fill `batchSender` field of `addresses` object with the deployed smart contract address.

##### scripts/peripherals/deployOrderBookReader.js
This script will deploy `OrderBookReader` contract. After deployment, fill `orderBookReader` field of `addresses` object with the deployed smart contract address.

##### scripts/peripherals/deployReader.js
This script will deploy `Reader` contract. After deployment, fill `reader` field of `addresses` object with the deployed smart contract address.

##### scripts/peripherals/deployRewardReader.js
This script will deploy `RewardReader` contract. After deployment, fill `rewardReader` field of `addresses` object with the deployed smart contract address.

##### scripts/peripherals/deployVaultReader.js
This script will deploy `vaultReader` contract. After deployment, fill `vaultReader` field of `addresses` object with the deployed smart contract address.

##### scripts/staking/deployStakeManager.js
This script will deploy `StakeManager` contract. After deployment, fill `stakeManager` field of `addresses` object with the deployed smart contract address.

##### scripts/staking/deployFlpWrappers.js
This script will deploy `StakedFlp` and `FlpBalance` contracts. After deployment, fill `stakedFlp` and `flpBalance` fields of `addresses` object with the deployed smart contract addresses.


If all the above scripts are run, the smart contracts are ready for exchange.


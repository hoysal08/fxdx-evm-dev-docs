# Fee Distribution

Fee withdrawal and distribution are done on a weekly basis.  
Fees are withdrawn and distributed by running some scripts in fxdx-contracts project.  

This documentation includes the following two sections:

 - [Local Environment Setup](#local-environment-setup)
 - [Run Scripts for Fee Withdrawal & Distribution](#run-scripts-for-fee-withdrawal--distribution)

## Local Environment Setup
1. clone [fxdx-evm-contracts](https://github.com/fxdxdex/fxdx-evm-contracts) repository from github.  
In terminal or git bash (on Windows), please run the following commands:  
```
cd <path\to\the\work\directory>
git clone https://github.com/fxdxdex/fxdx-evm-contracts.git
```

2. install `nvm`.  
You can install `nvm` by following guides in https://www.freecodecamp.org/news/node-version-manager-nvm-install-guide/.

3. install node version 16 by using `nvm`
```
nvm install 16
```

4. install node packages of `fxdx-evm-contracts` project.  
```
cd <path/to/the/fxdx-evm-contracts/directory>
git pull
nvm use
yarn
```

If you follow the above steps correctly, you will be ready to run fee withdrawal & distribution scripts

# Run Scripts for Fee Withdrawal & Distribution
In order to withdraw fees and distribute them, you should run the following 4 scripts.  
In the scripts, `<network>` should be replaced with one of networks keys in `hardhat.config.js` file such as `goerli`, `arbitrum`, `moonbeam`, etc.

Before start, you should open the terminal (Linux and MacOS) or git bash (windows).  
Then run the following commands:
```
cd path/to/fxdx-evm-contracts/directory
nvm use
```

### scripts/fees/withdrawFees.js  
This scripts will withdraw fees from the `vault` contract.
You can run this script by executing the following command:
```
npx hardhat run --network <network> scripts/fees/withdrawFees.js
```
After running this script, don't close the termal.  
Some important values will be printed into termal or git bash, and you should use those values in the following scripts.  
The values you will use starts with `-> Timestamp: ` or `----> <token name> fees:`.

### scripts/fees/calculateFeeDistribution.js
This script calculates the fee distribution parameters that will be used for `direct pool deposit` and `rewards distribution`.  
This script uses the values got by running `withdrawFees.js` script.

- First of all, open the `scritps/fees/calculateFeeDistribution.js` file in the code editor (e.g. Visual Studio Code).  

- go to `get<Netowork>Values` function definition (for example, `getGoerliValues`).  

- check and update `MAINTENENCE_BPS`, `DIRECT_POOL_BPS`, and `REWARDS_BPS` values. These values indicate how much of the token fees will be used for `maintenance`, `direct pool deposit` and `rewards`. The sum of these 3 values should be equal to `BASIS_POINTS_DIVISOR`.  

- update `TOKEN_FEES` object with the values printed when running `withdrawFees.js` script. replace values in `TOKEN_FEES` object with the one corresponding to the token name. For example, you can set `btc` value to the one started with `----> btc fees:` in the `withdrawFees.js` script result.

- update `WITHDRAW_TIMESTAMP` to the `-> Timestamp:` value in the `withdrawFees.js` script result.

- Then save the updates in the file and run the following command.
```
npx hardhat run --network <network> scripts/fees/calculateFeeDistribution.js
```
After running the script, don't close the terminal. You should used the values printed into the terminal.

- First, copy the whole `calculateFeeDistribution.js` script output in terminal and send it to the developer. He should update the `fxdx-evm-api` project with it.

- And keep it in the permant storage (any file in your machine or cloud storage). This file shows fee withdrawal history of the platform.

The script result will also be used in `directPoolDeposit.js` and `updateRewards.js` scripts (see the folloing sections).

### scritps/fees/directPoolDeposit.js
This script will deposit some portion of withdrawn fees directly to the platform pool. This process is necessary to keep `FLP` price in a certain range.

This script uses the calculation results got by running `calculateFeeDistribution.js` script.

- First of all, open the `scritps/fees/directPoolDeposit.js` file in the code editor (e.g. Visual Studio Code).  

- go to `get<Netowork>Values` function definition (for example, `getGoerliValues`).  

- update `DIRECT_POOL_AMOUNTS` object with the values printed when running `calculateFeeDistribution.js` script. replace values in `DIRECT_POOL_AMOUNTS` object with the one corresponding to the token name. For example, you can set `btc` value to the one started with `-> btc fees:` -> `   ** direct pool:` in the `calculateFeeDistribution.js` script result.

- Then save the updates in the file and run the following command.
```
npx hardhat run --network <network> scripts/fees/directPoolDeposit.js
```

### scripts/fees/updateRewards.js
This script transfers `rewards` part of the withdrawn fees to `rewardDistributor` contracts and updates `tokensPerInterval` state of `rewardDistributor` contracts.

- First of all, select the tokens for which you will distribute some portion (`REWARDS_BPS` in `calculateFeeDistribution.js` script) as rewards.

- For the selected tokens, get the rewards amount from `calculateFeeDistribution.js` script result (for example, `-> btc fees:` -> `   ** rewards  :` value) and swap them into `USDC`.

- open the `scritps/fees/updateRewards.js` file in the code editor (e.g. Visual Studio Code).  

- go to `get<Netowork>Values` function definition (for example, `getGoerliValues`).  

- check and update `FXDX_FEE_REWARDS_BPS` and `FLP_FEE_REWARDS_BPS`. These values indicates how much of the rewards will be distributed for `FXDX` stakeholders and `FLP` holders. The sum of these 2 values should be equal to `BASIS_POINTS_DIVISOR`.  

- update `TOKEN_REWARDS_IN_USDC` object values with the `USDC` amounts calculated in the second step.

- then save the updates in the file and run the following command.
```
npx hardhat run --network <network> scripts/fees/updateRewards.js
```

Fee withdrawal and distribution are done.  
After this process, you'd better check the native token balances of the following accounts:

- priceKeeper
- liquidator
- orderKeeper

These accounts should have plenty of native token balances in order to run automatic jobs.
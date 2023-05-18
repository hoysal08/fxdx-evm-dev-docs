# Smart Contracts
Repository: https://github.com/fxdxdex/fxdx-evm-contracts

This repository contains smart contracts, deployment scripts and test scripts.
It is based on HardHat framework and it requires Node version 16.

## Structure
It has 3 main folders and some configuration files:

 - [contracts](#contracts-folder): contains all smart contracts used for FXDX Exchange
 - [scripts](#scripts-folder): contains all scripts for smart contract deployment and smart contract state update
 - [test](#test-folder): contains all test scripts for smart contracts
 - env.json: contains PRC urls and deployer account private keys for the EVM-compatible chains on which FXDX Exchange is deployed.
 - hardhat.config.js: contains hardhat settings

### contracts folder
This folder contains all smart contracts for FXDX Exchange. You can see the details [here](contracts/ReadMe.md).

### scripts folder
This folder contrains all scripts for smart contract deployment and state update.
You can run the following command in order to run a script:
```
npx hardhat run --network <network> path/to/the/script
```
For example, in order to run scripts/core/deployVault.js for moonbeam chain,
```
npx hardhat run --network moonbeam scripts/core/deployVault.js
```
`<network>` is from `hardhat.config.js` file.

Running a single script is simple, but deploying all smart contracts and preparing them for exchange are very complicated processes. You can see the whole deployment procedure [here](#deployment-procedure).

### test folder
This folder contains all test scripts for smart contracts.

Unit and integrations tests are very important part in modern software engineering.
They are more critical in Solidity smart contract development because once a smart contract is deployed, we can't update it. We can update the smart contract states but not the smart contract itself. So we should write thorough test scripts for each smart contract.

You can see the whole test scripts in test folder. When you need to create or update the test script, you can check other test scripts and follow the similar structure.
The test scripts are based on `chai`, `ethereum-waffle` and `@nomiclabs/hardhat-waffle` packages.

In order to run the whole test scripts, you can run:
```
npx hardhat test
```
In order to run a specific test script, you can run:
```
npx hardhart test path/to/the/test/script
```
For example, in order to run `test/core/FlpManage.js`, you can run:
```
npx hardhat test test/core/FlpManager.js
```

## Deployment Procedure
Deploying all smart contracts and preparing them for exchange are very complicated processes.

You should deploy all smart contracts and update their states according to needs.

Many smart contracts depend on some smart contracts deployed before, and updating states of one smart contract can affect the behavior of other smart contracts.
So you should be very careful when deploying smart contracts and updating their states.

You can see the while deployment procedure [here](DeploymentProcedure.md).

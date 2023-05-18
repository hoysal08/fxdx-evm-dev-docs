# Liquidator
Repository: https://github.com/fxdxdex/fxdx-evm-liquidator

This repository contains the liquidator job for the EVM exchange platforms.  
It is based on Node and it requires Node version 16.

## Development Setup
After cloning the repository on the local environment, install packages by running the following command:
```
yarn
```
`yarn` is preferred in this project.

Create a `env.json` file in the root directory and add an JSON object with `LIQUIDATOR_PRIVATE_KEY`, `LIQUIDATOR_ADDRESS`, and `NETWORK`.  
`LIQUIDATOR_PRIVATE_KEY` and `LIQUIDATOR_ADDRESS` values should be the private key and address of the liquidator account.  
`NETWORK` value should be the name of the chain like `goerli`, `optimismGoerli`, `moonbeam`, etc.  

You can execute the job by running the following command:
```
node index.js
```
Then the liquidator is executed once, based on the current token prices.

## Structure
The project has the following main folder / files in its root directory:

- [`src` folder](#src-folder): contains all source code files for the liquidator job
- `env.json` file: contains `LIQUIDATOR_PRIVATE_KEY`, `LIQUIDATOR_ADDRESS`, and `NETWORK` to be used to construct and sign the liquidator transactions
- `index.js` file: entry point of the project. It executes the liquidator job.

### `src` folder
`src` folder has the following subfolders:

- abis: contains `abi` files for `PositionManager`, `Reader`, `Vault`, and `VaultReader` smart contracts. These `abi` files will be used to collect contract states and submit transactions for liquidation.
- [configs](#configs): contains a file for the project configuration. It loads `NETWORK` value from `env.json`.
- [data](#data): contains files that keep smart contract addresses and token information
- [graphql](#graphql): contains a graphql client definition used to collect data from the `positions` subgraph
- [helpers](#helpers): contains token and position helper functions used to calculate position liquidation statuses
- [jobs](#jobs): contains the liquidator job
- [utils](#utils): contains helper functions and JsonRpcProviders

#### configs
This folder contains a file for the project configuration.

`NETWORK` value indicates on which chain the project works. Possible values are `moonbeam`, `optimismGoerli` and `goerli` at the moment. If the exchange platform is deployed to other chains, there will be more options. It loads the value from `env.json`.

#### data
This folder contains files that keep smart contract addresses and token information for the exchange platforms.

- contracts.data.js: keeps smart contract addresses in the chains on which the exchange platforms work. It also provides some getter functions to help get contract addresses easily

- tokens.data.js: keeps information of the tokens in the chains on which the exchange platforms work. It also provides some getter functions to help get token information easily.

#### graphql
This folder contains graphql client definitions that will be used to collect data from `positions` subgraphs: `POSITIONS_SUBGRAPH_URLS`.

#### helpers
This folder contains token and position helper functions used to calculate position liquidation statuses.

- positions.helper.js: contains the helper functions to get active positions from the `positions` subgraph and calculate position liquidation statuses.

- tokens.helper.js: contains the helper functions to get `vault token info` abd construct `infoTokens` object. This object will be used in calculating position liquidation statuses.

#### jobs
This folder contains the liquidator job.  

This job fetches active positions from the `positions` subgraph first.  
Then it gets `infoTokens` object.  
After that, it gets position liquidation status for each position, and if liquidation condition is met, submits the position liquidation transaction to the chain.  
This job is triggerred by `index.js`.

#### utils
This folder contains some helper functions and JsonRpcProviders.

- numbers.utils.js: contains `bigNumberify` and `expandDecimals` functions to handle big numbers.
- providers.utils.js: defines `JsonRpcProvider`s that will be used to fetch data from smart contracts and call smart contract methods (transactions).

## Production Deployment
### New deployment
1. Create a new EC2 instance in the AWS account.  
`Unbuntu LTS 22.04` OS and `t2.medium` instance are preferred. All the following terminal commands assume that the instance OS is `Ubuntu LTS 22.04`.

2. connect to the server via `ssh`.
```
ssh -i <path/to/the/key/file> ubuntu@<server ip address>
```

3. install `Node` 16 and `yarn` package  
run the following commands in order:
```
sudo apt update
cd ~
curl -sL https://deb.nodesource.com/setup_16.x -o nodesource_setup.sh
sudo bash nodesource_setup.sh
sudo apt install nodejs
sudo npm i -g yarn
```
You can check the installed Node version by `node -v`.

4. clone `fxdx-evm-liquidator` repository  
```
cd ~
sudo git clone https://github.com/fxdxdex/fxdx-evm-liquidator.git
cd fxdx-evm-liquidator
sudo chown -R $USER:$USER /home/ubuntu/fxdx-evm-liquidator
sudo chmod -R 755 /home/ubuntu/fxdx-evm-liquidator

nano env.json      // add a JSON object with necessary environment variables and save it

yarn
```
You can run `node index.js` to check if environment is set up correctly.

5. add a command to `cron`  
in order to add a command to `cron`:
```
crontab -e
```
Add the following command to the bottom of the opened file and save it:
```
* * * * * /usr/bin/node /home/ubuntu/fxdx-evm-liquidator/index.js >> /dev/null 2>&1
```
By this command, `cron` will execute liquidator job every minute. You can change the interval by editing the first `* * * * *` part.  

#### Server Update
1. connect to the server via `ssh`.
```
ssh -i <path/to/the/key/file> ubuntu@<server ip address>
```
2. go to the server directory and pull the updates
```
cd /home/ubuntu/fxdx-evm-liquidator
git pull
```
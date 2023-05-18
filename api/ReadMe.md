# Backend API & Price Submitter & Position Keeper
Repository: https://github.com/fxdxdex/fxdx-evm-api

This repository contains backend API, price submitter and position keeper jobs.
It is based on Node / Express and it requires Node version 16.

## Development Setup
After cloning the repository on the local environment, install packages by running the following command:
```
yarn
```
`yarn` is preferred in this project.

Create a `.env` file in the root directory and add `NETWORK`, `PORT`, `PRICE_SUBMITTER_PRIVATE_KEY`, `MONGODB_URL` and `MAX_PRICE_INTERVAL` fields.  
- `NETWORK` is the name of the chain for which the API works. Its vaule can be `goerli`, `optimismGoerli`, `moonbeam`, etc.
- `PORT` indicates which port the server will listen. Default value is 3000.
- `PRICE_SUBMITTER_PRIVATE_KEY` should be the private key of the price sender account.
- `MAX_PRICE_INTERVAL` is the maximum interval in which prices are submitted to the platform.

In order to host the server locally, you can run the following command:
```
node index.js
```
Then the server listens on port 3000 and you can call API endpoints at http://localhost:3000.
By running this command, `Price Submitter & Position Keeper` job is also triggerred.

## Structure
The project has the following main folder / files in its root directory:

- [`src` folder](#src-folder): contains all source code files for backend API, price submitter and position keeper logic
- `.env` file: contains `NETWORK`, `PORT`, `PRICE_SUBMITTER_PRIVATE_KEY`, `MONGODB_URL` and `MAX_PRICE_INTERVAL` to be used for platform API configuration.
- `index.js` file: entry point of the project. It triggers collectors and jobs, and runs the REST API server (Express app).

### `src` folder
`src` folder has the following subfolders:

- abi: contains `abi` files for `FastPriceFeed`, `SwapRouter`, `LiquidityRouter` and `PositionRouter` smart contracts. These `abi` files will be used to collect contract states and submit transactions for price submission and position / swap / liquidity request execution
- [collectors](#collectors): contains data collectors to reduce the server load and improve the response time
- [configs](#configs): contains a file for the project configuration
- [controllers](#controllers): contains files for handling some API requests
- [data](#data): contains files that keep smart contract addresses, token information and fees history for the exchange platforms
- [graphql](#graphql): contains graphql client definitions that will be used to collect data from `actions`, `prices`, `admin` and `stats` subgraphs
- [jobs](#jobs): contains the price submitter and position / swap / liquidity request keeper job
- [middlewares](#middlewares): contains a middleware definition for logging
- `models`: contains `PriceCandle` model used to submit token prices to `mongodb`.
- [routes](#routes): contains a file to define API endpoints and route requests to the corresponding handlers
- [utils](#utils): contains helper functions, price getter functions and JsonRpcProviders

#### collectors
This folder contains data collectors to reduce the server load and improve the response time.  
In this project, API endpoints collect the response data from subgraphs and external APIs. And several requests doesn't have query params and they are called frequently from the web apps.  
So in order to reduce the calls to subgraphs and external APIs, and respond quickly, the necessary data is collected in a certain interval and kept in memory. When the corresponding API endpoints are called, the data in memory is returned.  
This way we can reduce the network traffic and cost, and improve the response time.

- admin-actions.collector.js: collects `timelockActions` and `tokenManagerActions` data from `admin` subgraph every 2 seconds and keep it to `ADMIN_ACTIONS`.
- hourly-volume.collector.js: collects hourly volume data for the last 24 hours every 10 seconds and keeps it to `HOURLY_VOLUMES`.
- positionstats.collector.js: collects position stats (`totalLongPositionSizes` and `totalShortPositionSizes`) data every 4 seconds and keeps it to `POSITION_STATS`.
- prices.collector.js: collects token prices every 2 seconds and keeps them to `TOKEN_PRICES`.
- total-volume.collector.js: collects total volume data every 4 seconds and keeps it to `TOTAL_VOLUME`.
- index.js: has `executeCollectors` function that triggers collectors.

#### configs
This folder contains a file for the project configuration.

`NETWORK` value indicates on which chain the project works. Possible values at the moment are `moonbeam` and `goerli`. If the exchange platform is deployed to other chains, there will be more options. It loads the value from `NETWORK` field of `.env` file.

#### controllers
This folder contains files for handling some API requests.

- actions.controller.js: has `getActions` function that handles `/actions` endpoint. It gets the data from `actions` subgraph and returns it.

- fees.controller.js: has `getFeesSummary` function that handles `/fees_summary` endpoint. It returns weekly fees history from `fees.data.js`.

- orders.controller.js: has `getOrderIndices` function that handles `/orders_indices` endpoint. It gets the data from `stats` subgraph and returns it.

#### data
This folder contains files that keep smart contract addresses, token information and fees history for the exchange platforms.

- contracts.data.js: keeps smart contract addresses in the chains on which the exchange platforms work. It also provides some getter functions to help get contract addresses easily

- fees.data.js: keeps weekly fees history that will be fetched by `/fees_summary` endpoint.

- tokens.data.js: keeps token addresses in the chains on which the exchange platforms work. It also provides some getter functions to help get token addresses easily.

#### graphql
This folder contains graphql client definitions that will be used to collect data from `actions`, `admin`, `prices` and `stats` subgraphs: `STATS_SUBGRAPH_URLS`, `ADMIN_SUBGRAPH_URLS`, `PRICES_SUBGRAPH_URLS`, and `ACTIONS_SUBGRAPH_URLS`.

#### jobs
This folder contains the price submitter and position / swap / liquidity request keeper job.  
Price submitter and position / swap / liquidity request keeper logic is fulfilled by one job because both actions is done by running methods of `FastPriceFeed` contract. And position keeper logic also contains price submission logic.

This job defines `PRICE_FETCH_INTERVAL`, `MAX_INTERVAL`, and `POSITION_KEEPER_INTERVAL`.

- `PRICE_FETCH_INTERVAL`: prices are fetched from 6 price feed sources and kept in mongodb `pricecandles` collection once per this interval.
- `MAX_INTERVAL`: If time gap between the last price submission time and the current time is more than this interval, prices are submitted to the `FastPriceFeed`.
- `POSITION_KEEPER_INTERVAL`: position / swap / liquidity requests are fetched from `PositionRouter`, `SwapRouter`, and `LiquidityRouter` contracts once per this interval. If there are new requests, new prices are submitted and position / swap / liquidity requests are executed.

As mentioned above, prices are checked every `PRICE_FETCH_INTERVAL` and if `MAX_INTERVAL` is met, prices are submitted to `FastPriceFeed`. Position / swap / liquidity requests are checked every `POSITION_KEEPER_INTERVAL`, and if there are new position / swap / liquidity requests, they are executed.

This job is triggerred when the server is started (index.js).

#### middlewares
This folder contains an API middleware definition for logging.
By this middleware, every API requests are logged into terminal.

#### routes
This folder contains a file to define API endpoints and route requests to the corresponding handlers.

- `/ui_version`: returns the current UI version of the platform. It will be used to let users refresh the web app when they use the old version

- `/fxdx_supply`: returns the total supply of `FXDX` token. Because `FXDX` token is not enabled yet, it just returns "0" at the moment.

- `/order_indices`: calls `getOrderIndices` function from `orders.controller.js` in order to return order indices of a specified account.

- `/actions`: calls `getActions` function from `actions.controller.js` in order to return all actions or the actions of a specified account.

- `/position_stats`: returns `POSITION_STATS` object collected in `positionstats.collector.js`. `POSITION_STATS` contains `totalLongPositionSizes` and `totalShortPositionSizes` fields.

- `/total_volume`: returns `TOTAL_VOLUME` object collected in `total-volume.collector.js`.

- `/hourly_volume`: returns `HOURLY_VOLUMES` collected in `hourly-volume.collector.js`. It contains volumes in last 24 hours.

- `/prices`: returns token prices collected in `prices.collector.js`.

- `/fees_summary`: calls `getFeesSummary` function from `fees.controller.js` in order to return fees weekly withdrawal history.

- `/admin_actions`: returns `ADMIN_ACTIONS` object collected in `admin-actions.collector.js`.

#### utils
This folder contains helper functions, price getter functions and JsonRpcProviders.

- helpers.utils.js: contains helper functions used in other source code files
- prices.utils.js: contains the functions to collect token prices from 6 well known price feeds: `Binance`, `Bitfinex`, `Kraken`, `Kucoin`, `Coinbase` and `Bitstamp`.
- providers.utils.js: defines `JsonRpcProvider`s that will be used to fetch data from smart contracts and call smart contract methods (transactions).

## Production Deployment
### New deployment
1. Create a new EC2 instance in the AWS account.  
`Unbuntu LTS 22.04` OS and `t2.medium` instance are preferred. All the following terminal commands assume the instance OS is `Ubuntu LTS 22.04`.

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

4. install `nginx` to the server.  
run the following commands in order:
```
sudo apt update
sudo apt install nginx

sudo ufw allow 'Nginx Full'
sudo ufw allow 'OpenSSH'
sudo ufw allow ssh
sudo ufw enable
sudo ufw status

sudo systemctl enable nginx
```
check nginx status by running:
```
systemctl status nginx
```
If you enter `http://<server ip address>` in the browser's address bar, you can see the server running.

5. clone `fxdx-evm-api` repository
```
cd /var/www
sudo git clone https://github.com/fxdxdex/fxdx-evm-api.git
cd fxdx-evm-api
sudo chown -R $USER:$USER /var/www/fxdx-evm-api
sudo chmod -R 755 /var/www/fxdx-evm-api

nano .env      // add necessary environment variables and save it

yarn
```
You can run `node index.js` to check if the server is set up correctly.

6. install `pm2`  
run the following commands in order:  
(make sure you are in `/var/www/fxdx-evm-api`)
```
sudo npm install pm2@latest -g
pm2 start index.js
pm2 startup systemd

sudo env PATH=$PATH:/usr/bin /usr/lib/node_modules/pm2/bin/pm2 startup systemd -u ubuntu --hp /home/ubuntu

pm2 save
sudo systemctl start pm2-ubuntu
```

7. domain setting  
run the following command:
```
sudo nano /etc/nginx/sites-available/<your domain>
```
add the following content to it and save the updates.
```
server {
    listen 80;
    listen [::]:80;

    root /var/www/fxdx-evm-api;
    index index.html index.htm index.nginx-debian.html;

    server_name <your_domain>;

    location / {
        proxy_pass http://localhost:3000;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection 'upgrade';
        proxy_set_header Host $host;
        proxy_cache_bypass $http_upgrade;
    }
}
```
Then please run the following commands
```
sudo ln -s /etc/nginx/sites-available/<your domain> /etc/nginx/sites-enabled/

sudo nginx -t
sudo systemctl restart nginx
```

8. enable `https`  
run the following commands in order to enable `https` with `Let's Encrypt`:
```
sudo apt-get update
sudo apt-get install python3-certbot-nginx

sudo certbot --nginx -d <your domain>
```

Then you can see the server running on `https://<your domain>`.

#### Server Update
1. connect to the server via `ssh`.
```
ssh -i <path/to/the/key/file> ubuntu@<server ip address>
```
2. go to the server directory and pull the updates
```
cd /var/www/fxdx-evm-api
git pull
```
3. restart the server
```
pm2 stop index
pm2 start index.js
```
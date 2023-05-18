# Stats API and Web App
Repository: https://github.com/fxdxdex/fxdx-evm-stats

This repository contains stats API endpoints and web app for Fxdx Exchange on EVM-compatible blockchains.
It is based on Node / Express and it requires Node version 14.

## Development Setup
After cloning the repository on the local environment, install packages by running the following command:
```
yarn
```
`yarn` is preferred in this project.

In order to host the server locally, you can run the following command:
```
yarn start
```
Then the server listens on port 3113 and you can open the web app at http://localhost:3113 and call API endpoints at http://localhost:3113/api/.

In order to trigger the price getter job, you can add `ENABLE_PRICES=true` at the start of `package.json -> scripts -> start` and then run `yarn start`.

## Structure
The project has the following main folder / files in its root directory:

- `abis` folder: contains smart contract API files used to get smart contract states data
- `public` folder: contains `favicon` and `robots.txt` used for the stats web app
- [`src` folder](#src-folder): contains all source code files for stats API and web app logic

### `src` folder
`src` folder has the following subfolders and files:

- [components](#components): contains React components for various charts.
- `fonts`: contains `relative` and `roboto` font files for web app.
- `hooks`: contains `useChartViewState` hook for chart components.
- `img`: contains logo files for the stats web app.
- [views](#views): contains stats web app page components.
- `addresses.js`: contains token and some smart contract addresses and a function to retrieve them easily.
- `App.css`: contains css definitions for web app styles.
- `App.js`: the main web app component. It defines web app routes and header.
- `App.test.js`: `App` component test file.
- `client.js`: the entry point for the web app. It initializes the web app and load `App` component.
- `config.js`: contains the configuration parameters for the project. At the moment, only one parameter, `IS_MAINNET` is used. When it is true, the web app shows mainnet pages, and when it is false, the web app shows testnet pages. It also affects the price getter job.
- `dataProvider.js`: contains getter functions and hooks used to retrieve stats data from `stats` subgraphs, APIs and blockchain.
- `graph.js`: contains graphql client definitions used to retrieve token prices data from `prices` subgraphs. It also provides a getter function to get the graphql client for the specified chainId.
- `helper.js`: contains helper functions used for the web app and API.
- `index.js`: the entry point of the project. It creates and runs the server when the project is start.
- `middlewares.js`: contains `requestLogger` and `csp` middleware definitions for the `Express` app.
- [prices.js](#pricesjs): contains all functions for the price getter job, and triggers the price getter job when the project is started.
- [routes.js](#routesjs): defines routes for the stats API and web app.
- `server.js`: defines `Express` app for the web app and API.
- `ttl-cache.js`: defines `TtlCache` used in the price getter job.
- `utils.js`: contains utility functions used for the web app and API.

#### components
This folder contains React components for various charts.

- `PoolAmountChart` displays a chart that shows `Pool Composition` history
- `TradersProfitLossChart` displays a chart that shows `Traders Profit vs. Loss` chart
- `ChartWrapper.js` is a component that wraps the chart. It shows chart title, csv link and `%` toggle button at the top of the wrapped chart.
- `CsvLink` is a icon button used to download the chart data in the `csv` format.
- `DateRangeSelect` is a customized date range select component used to select the date range for the charts in web pages
- `FeesChart` displays a chart that shows `Fees` history.
- `GenericChart` is a component that generates a chart based on the provided data. It is used to display charts for `Open Interest`, `Unique Users`, `New Users`, `Borrowing Rate Annualized`, `Swap Sources`, etc.
- `VolumeChart` displays a chart that shows `Volume` history

#### views
This folder contains stats web app page components.

- `Arbitrum` shows the stats charts for the exchange on `Arbitrum` chain.
- `Avalanche` shows the stats charts for the exchange on `Avalanche` chain.
- `Goerli` shows the stats charts for the exchange on `Goerli` chain.
- `MoonBeam` shows the stats charts for the exchange on `MoonBeam` chain.
- `Referrals` shows the referrals stats charts for the exchange on the specified chain. The chain name is sent as a query parameter.

#### prices.js
This file contains all functions for the price getter job, and triggers the price getter job when the project is started.

It retrieves new token price candles from `prices` subgraph once per `LOAD_NEW_PRICES_LOOP_INTERVAL` and old price candles once per `LOAT_OLD_PRICES_LOOP_INTERVAL`.

It defines some helper functions for the price getter job and some other helper functions to get price candles easily for API requests.

#### routes.js
This file defines routes for the stats API and web app.

- `/api/earn/:account`: retrieves the staking and earn data from smart contracts for the specified account and returns it.
- `/api/fxdx-supply`: retrieves the `FXDX` supply from Fxdx API and returns it.
- `/api/candles/:symbol`: get `symbol` price cancles from price getter job for the specified `chain` and `period`. `chain` is specified by `preferableChainId` query param and `period` is specified by `period` param.
- It routes all other traffics to the web app

## Production Deployment
### New deployment
1. Create a new EC2 instance in the AWS account.  
`Unbuntu LTS 22.04` OS and `t2.medium` instance are preferred. All the following terminal commands assume the instance OS is `Ubuntu LTS 22.04`.

2. connect to the server via `ssh`.
```
ssh -i <path/to/the/key/file> ubuntu@<server ip address>
```

3. install `Node` 14 and `yarn` package  
run the following commands in order:
```
sudo apt update
cd ~
curl -sL https://deb.nodesource.com/setup_14.x -o nodesource_setup.sh
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

5. clone `fxdx-evm-stats` repository
```
cd /var/www
sudo git clone https://github.com/fxdxdex/fxdx-evm-stats.git
cd fxdx-evm-api
sudo chown -R $USER:$USER /var/www/fxdx-evm-stats
sudo chmod -R 755 /var/www/fxdx-evm-stats

nano .env      // add necessary environment variables and save it

yarn
yarn build
```
After build, you can run `node build/server.js` to check if the server is set up correctly.

6. install `pm2`  
run the following commands in order:  
(make sure you are in `/var/www/fxdx-evm-stats`)
```
sudo npm install pm2@latest -g
pm2 start build/server.js
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

    root /var/www/fxdx-evm-stats;
    index index.html index.htm index.nginx-debian.html;

    server_name <your_domain>;

    location / {
        proxy_pass http://localhost:8080;
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

9. enable `gzip` on `nginx`  
In order to speed up stats web app loading, you can enable `gzip` on `nginx`.  
run the following commands in order:
```
sudo nano /etc/nginx/nginx.conf
```
Then find the `gzip` related section and update the content as follows:
```
##
# `gzip` Settings
#
#
gzip on;
gzip_disable "msie6";

gzip_vary on;
gzip_proxied any;
gzip_comp_level 6;
gzip_buffers 16 8k;
gzip_http_version 1.1;
gzip_min_length 256;
gzip_types
  application/atom+xml
  application/geo+json
  application/javascript
  application/x-javascript
  application/json
  application/ld+json
  application/manifest+json
  application/rdf+xml
  application/rss+xml
  application/xhtml+xml
  application/xml
  font/eot
  font/otf
  font/ttf
  image/svg+xml
  text/css
  text/javascript
  text/plain
  text/xml;
```
save the updates and then run the following command:
```
sudo systemctl restart nginx
```

#### Server Update
1. connect to the server via `ssh`.
```
ssh -i <path/to/the/key/file> ubuntu@<server ip address>
```
2. go to the server directory and pull the updates
```
cd /var/www/fxdx-evm-stats
git pull
yarn build
```
3. restart the server
```
pm2 stop server
pm2 start build/server.js
```
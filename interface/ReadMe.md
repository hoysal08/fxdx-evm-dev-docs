# Main Interface
Repository: https://github.com/fxdxdex/fxdx-evm-interface

This repository contains main interface for Fxdx Exchange on EVM-compatible blockchains.
It is based on React and it requires Node version 16.

## Development Setup
After cloning the repository on the local environment, install packages by running the following command:
```
yarn
```
`yarn` is preferred in this project.

In order to run the web app locally, execute the following command:
```
yarn start
```
Then you can open the web app at http://localhost:3030.

## Structure
The project has the following main folders in its root directory:

- `public` folder: contains `favicon`, `index.html`, `robots.txt` and some other image files used for the web app
- [`src` folder](#src-folder): contains all source code files for the web app logic

### `src` folder
`src` folder has the following subfolders and files:

- `_tests_` folder: contains sample test for `getLiquidationPrice` function.
- `abis` folder: contains smart contract API files used to get smart contract states data and call smart contract methods (transactions).
- `App` folder: contains `App` component. It defines web app routes, header, some modals and styles used in the whole app.
- [`components` folder](#components): contains React components used in the web app
- [`config` folder](#configs): contains configuration files that defines configuration parameters used in the web app
- [`domain` folder](#domain): contains helper functions used for specific purposes in the web app
- `fonts`: contains `inter`, `relative` and `roboto` font files for web app.
- `hooks`: contains `useChartViewState` hook for chart components.
- `img`: contains logo files and images used in the web app.
- [`lib` folder](#lib): contains general helper functions used in components and pages.
- `locales` folder: contains `lingui` scripts to support translations.
- [`pages` folder](#pages): contains the web app page components.
- `styles` folder: contains `css` files for Font, Input and Shared styles
- `index.tsx`: the entry point for the web app. It initializes the web app and load `App` component.

#### components
This folder contains React components used in the web app pages.

- `AddressDropdown` is a dropdown menu button used to display `Copy Address`, `View in Explorer` and `Disconnect` options in the app header when the user account is connected.
- `APRLabel` is a component that displays staking and earn data when chainId and label are given. It is used in `TokenCard` component.
- `BuyInputSection` is a component used to enter token or `FLP` amount in `FlpSwap` component.
- `CheckBox` is a customized checkbox component. It is widely used in the web app.
- `Common` folder contains `Button`, `Card`, `ConnectWalletButton`, `Loader`, `Portal`, `SEO` and `SpinningLoader` components that are used widely throughout the app.
- `EventToast` folder contains components for `EventToast` and `useEventToast`. This toast is used to inform important events.
- [`Exchange` folder](#exchange-folder) contains components used mainly in `Trade` page.
- `ExternalLink` is a component to display external links to other websites.
- `Flp` folder contains `FlpSwap` and `SwapErrorModal` components that are used in Buy / Sell Liquidity(FLP) pages.
- `Footer` is a component to display footer of the web app for all pages.
- `Header` folder contains `AppHeaderLinks`, `AppHeaderUser`, `Header`, and `HeaderLink` components used to display the web app header part.
- `InputSection` is a component used to input token amounts.
- `Modal` folder contains `Modal` components used to display modals in web app pages.
- `ModalViews` folder contains `RedirectModal` component used to display a model when users go to Trading app from the Home page.
- `NetworkDropDown` is a component to display network switch options and links to language and setting modals. It is used in the web app header.
- `Overlay` is a component to display overlaid contents on the top of another component.
- `Radio` is a component to show a group of radio options.
- `Referrals` folder contains `AddAffiliateCode`, `AffiliatesStats`, `EmptyMessage`, `InfoCard`, `JoinReferralCode`, and `TradersStats` components and referrals helper functions for the referral actions. They are used in the `Referrals` page.
- `StatsTooltip` folder contains `StatsTooltip` and `StatsTooltipRow` components used to display stats values with tooltips.
- `Tab` is a component used to display tab options.
- `ToastifyDebug` is a toast component used for debug purposes. When a transaction fail and there was an unknown issue, this toast is popped up and you can see the error details there.
- `TokenCard` is a component used to display `FXDX` and `FLP` token info and buy links in `Home` and `Buy` pages.
- `Tooltip` folder contains `Tooltip` and `TooltipWithPortal` components used to display contents with tooltips.

##### `Exchange` folder
This folder contains components used mainly in `Trade` page.

- `ChartTokenSelector` is a component used to select chart token for which the price chart will be drawn.
- `ConformationBox` is a modal that shows summarized information before trade transactions are submitted to the chain. It is used for `Swap`, `CreateSwapOrder`, `IncreasePosition`, and `CreateIncreaseOrder` actions.
- `ExchangeBanner` is a component to show a banner at the top of `Trade` page.
- `ExchangeInfoRow` is a component to display `label`-`amount` pair as a row in the components like `SwapBox`, `ConfirmationBox`, etc.
- `ExchangeTVChart` is a component to show price cancle chart for tokens.
- `ExchangeWalletTokens` is a component to disploy wallet tokens in `Trade` page. It is hidden at the moment.
- `OrderEditor` is a modal used to edit `limit` or `stop` orders.
- `OrdersList` is a component to display list of active orders in `Trade` and `Actions` page.
- `OrdersToa` is a modal component to show warnings before creating `limit` or `stop` orders.
- `PositionDropdown` is a dropdown component used in position row of `PositionsList` component in order to show `Edit Collateral`, `Select Market` and `Share Position` options.
- `PositionEditor` is a modal to support `deposit` / `withdraw` of position collateral.
- `PositionSeller` is a modal to support `decrease` or `close` positions. It supports both `market` and `stop` orders.
- `PositionShare` is a modal to share position with others.
- `PositionsList` is a component to display list of positions and related actions / modals. It is used in `Trade` and `Actions` page to display the list of active positions.
- `SwapBox` is a component used to enter values for trading and trigger various `market` and `limit` orders. It is a core component in `Trade` page.
- `TokenSelector` is a component to allow users to select tokens for `trading` and `buy/sell` liquidity.
- `TradeHistory` is a component to show the list of trader actions in the chronological order. It is used in `Trade` and `Actions` pages.

#### configs
This folder contains configuration files that defines configuration parameters used in the web app.

- `backend.ts` file contains stats API urls and backend API urls used in the web app. It also provides some helper functions to easily get the required urls.
- `chains.ts` file contains supported chain ids, chain infos, rpc urls, network metadata and some getter functions. This file contains all urls and infos to connect to the chains.
- `contracts.ts` file contains smart contract addresses on the supported chains and some getter functions.
- `events.ts` file contains events data for the platform.
- `localStorage.ts` file contains key contains used to keep the user options to localStorage.
- `subgraph.ts` file contains `stats` and `referrals` subgraph urls.
- `tokens.ts` file contains information of the tokens used in the supported chains. It also provides some getter functions to get tokens easily.
- `ui.ts` file contains some constants used in UI components

#### domain
This folder contains helper functions used for specific purposes in the web app.

- `tokens` folder contains all types, hooks and utility functions related to the tokens in the platform.
- `common.js` contains `get1InchSwapUrl` function.
- `legacy.js` contains `hooks` and helper functions used thoughout the app. It mainly contains the functions from old version of the platform.
- `prices.js` contains `useChartPrices` hook and helper functions used in the hook.
- `referrals.js` contains `hooks` and helper functions used for referrals.
- `useFeesSummary.js` contains `useFeesSummary` hook definition.
- `useTotalVolume.js` contains `useTotalVolume` hook definition.

#### lib
This folder contains general helper functions used in components and pages.

- `chains` folder contains `useChainId` hook definition.
- `contracts` folder contains helper functions to fetch information from smart contracts and call smart contract methods (transactions).
- `localStorage` folder contains `useLocalStorageByChainId` and `useLocalStorageSerializeKey` hooks.
- `rpc` folder contains `getProvider` and `getFallbackProvider` helper functions.
- `subgraph` folder contains subgraph graphql client definitions and some helper functions to get the specified client easily.
- `wallets` folder contains `hooks` and helper functions related to wallet connection, network addition and switch.
- `dates.ts` contains the functions related to time and dates.
- `downloadImage.ts` contains `downloadImage` function used to download data blob specified by dataURI parameter.
- `helperToast.ts` contains `helperToast` definition.
- `i18n.js` contains helper functions for translation
- `legacy.js` contains hooks and helper functions from the old version of the platform.
- `number.ts` contains helper functions used to handle `BigNumber`s.
- `sleep.js` contains `sleep` helper function.
- `url.ts` contains `getRootUrl` helper function.
- `useDebounce.js`, `useEffectDebugger.js`, `useLoadImage.js`, `useLockBodyScroll.ts`, `usePrevious.js`, `useRouteQuery.js`, and `useScrollToTop.js` files contain `useDebounce`, `useEffectDebugger`, `useLoadImage`, `useLockBodyScroll`, `usePrevious`, `useRouteQuery`, and `useScrollToTop` hook definitions.

#### pages
This folder contains the web app page components.

- `Actions` page shows user trade action history in chronological order. The url for this page is `/actions` or `/actions/:account`. When `account` param is specified, positions, orders and trade actions for the specified account is shown.
- `BeginAccountTransfer` page signals account transfer. `Account transfer` transfers all staking and rewarding data from the current accoutn to a new account.
- `Buy` page displays `TokenCard`s for `FXDX` and `FLP`.
- `BuyFlp` page displays `FLP` balance information for the connected account and system pool status and lets users buy or sell FLP tokens.
- `ClaimEsFxdx` page allows users to claim `EsFXDX` token.
- `CompleteAccountTransfer` page allows users to complete account transfer started in `BeginAccountTransfer` page.
- `Dashbaord` page displays platform overview, total stats, FXDX and FLP stats, and pool amounts of the platform tokens and their usage information. It also contains link to `stats` web app.
- `Ecosystem` page displays all projects managed by FXDX community. It is not available yet on the current active platforms.
- `Exchange` is the main `Trade` page. All trader works is done in this page. It displays token price chart, connected user positions, orders and trade actions history. It also contains `SwapBox` component which allows users to create long / short positions and swap tokens.
- `Home` is the home page of the platform. It is not used at the moment. A separate project is used for the `fxdx.exchange` home page.
- `Jobs` page shows all jobs that need new hires.
- `NftWallet` page allows users to transfer `Nft` to another account.
- `OrdersOverview` page shows all active orders in the platform. `orderKeeper` can execute orders in this page directly.
- `PageNotFound` page is used when users enter undefined routes in the browser's address bar.
- `PositionsOverview` page shows all active positions in the platform.
- `Referrals` page allows users to manage referral codes.
- `ReferralTerms` page shows terms and conditions for referrals in FXDX Exchange.
- `Stake` page allows users to stake FXDX, MultiplierPoints and EsFXDX, buy or sell FLP, and vest esFXDX to get FXDX tokens. Stakeholders and liquidity owners get the rewards on this page.
- `TermsAndConditions` page shows terms and conditions for FXDX Exchange Platform.

## Production Deployment
### New deployment
1. create a new EC2 instance in the AWS account.  
`Unbuntu LTS 22.04` OS and `t2.medium` instance are preferred. All the following terminal commands assume the instance OS is `Ubuntu LTS 22.04`.

2. connect to the server via `ssh`.
```
ssh -i <path/to/the/key/file> ubuntu@<server ip address>
```

3. install `nginx` to the server.  
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

4. create `/var/www/fxdx-interface` folder
```
sudo mkdir -p /var/www/fxdx-interface
sudo chown -R $USER:$USER /var/www/fxdx-interface
sudo chmod -R 755 /var/www/fxdx-interface
```

5. domain setting  
run the following command:
```
sudo nano /etc/nginx/sites-available/<your domain>
```
add the following content to it and save the updates.
```
server {
    listen 80;
    listen [::]:80;

    root /var/www/fxdx-interface;
    index index.html index.htm index.nginx-debian.html;

    server_name <your_domain>;

    add_header X-Frame-Options SAMEORIGIN always;

    location / {
        try_files $uri /index.html;
    }
}
```
Then please run the following commands
```
sudo ln -s /etc/nginx/sites-available/<your domain> /etc/nginx/sites-enabled/

sudo nginx -t
sudo systemctl restart nginx
```

6. enable `https`  
run the following commands in order to enable `https` with `Let's Encrypt`:
```
sudo apt-get update
sudo apt-get install python3-certbot-nginx

sudo certbot --nginx -d <your domain>
```

7. enable `gzip` on `nginx`  
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

8. build the web app in the local dev environment.  
run the following command in order to get the production build
```
yarn build
```

9. transfer build files to the server via `scp`.  
run the following command in order to transfer build files to the server:
```
scp -i <path/to/the/key/file> -r <path/to/the/project>/build/* ubuntu@<server ip address>:/var/www/fxdx-interface
```

Then you can see the server running on `https://<your domain>`.

#### Server Update
1. get the production app build files.  
```
yarn build
```

2. transfer build files to the server via `scp`.  
run the following command in order to transfer build files to the server:
```
scp -i <path/to/the/key/file> -r <path/to/the/project>/build/* ubuntu@<server ip address>:/var/www/fxdx-interface
```
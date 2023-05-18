# Admin Portal
Repository: https://github.com/fxdxdex/fxdx-evm-admin

This repository contains admin portal web app for Fxdx Exchange on EVM-compatible blockchains.
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

- `abis` folder: contains smart contract API files used to get smart contract states data and call smart contract methods (transactions).
- `App` folder: contains `App` component. It defines web app routes, header, some modals and styles used in the whole app.
- [`components` folder](#components): contains React components used in the web app
- [`config` folder](#configs): contains configuration files that defines configuration parameters used in the web app
- [`domain` folder](#domain): contains helper functions used for specific purposes in the web app
- `fonts`: contains `inter`, `relative` and `roboto` font files for web app.
- `img`: contains logo files and images used in the web app.
- [`lib` folder](#lib): contains general helper functions used in components and pages.
- `locales` folder: contains `lingui` scripts to support translations.
- [`pages` folder](#pages): contains the web app page components.
- `styles` folder: contains `css` files for Font, Input and Shared styles
- `index.tsx`: the entry point for the web app. It initializes the web app and load `App` component.

#### components
This folder contains React components used in the web app pages.

- `AddressDropdown` is a dropdown menu button used to display `Copy Address`, `View in Explorer` and `Disconnect` options in the app header when the user account is connected.
- `CheckBox` is a customized checkbox component. It is widely used in the web app.
- `Common` folder contains `Button`, `Card`, `ConnectWalletButton`, `Loader`, `Portal`, `SEO` and `SpinningLoader` components that are used widely throughout the app.
- `ExternalLink` is a component to display external links to other websites.
- `FastPriceFeedSettings` folder contains modals used to update `fastPriceFeed` contract states.
- `Footer` is a component to display footer of the web app for all pages.
- `Header` folder contains `AppHeaderLinks`, `AppHeaderUser`, `Header`, and `HeaderLink` components used to display the web app header part.
- `InlineActionButton` is an icon button component used to open a corresponding modal to set a smart contract state.
- `InputSection` is a component used to input token amounts.
- `Modal` folder contains `Modal` components used to display modals in web app pages.
- `NetworkDropDown` is a component to display network switch options and links to language and setting modals. It is used in the web app header.
- `Tab` is a component used to display tab options.
- `TimelockSettings` folder contains modals used to update `timelock` contract states.
- `ToastifyDebug` is a toast component used for debug purposes. When a transaction fail and there was an unknown issue, this toast is popped up and you can see the error details there.
- `Tooltip` folder contains `Tooltip` and `TooltipWithPortal` components used to display contents with tooltips.
- `VaultPriceFeedSettings` folder contains modals used to update `vaultPriceFeed` contract states.
- `VaultSettings` folder contains modals used to update `vault` contract states.

#### configs
This folder contains configuration files that defines configuration parameters used in the web app.

- `backend.ts` file contains stats API urls and backend API urls used in the web app. It also provides some helper functions to easily get the required urls.
- `chains.ts` file contains supported chain ids, chain infos, rpc urls, network metadata and some getter functions. This file contains all urls and infos to connect to the chains.
- `contracts.ts` file contains smart contract addresses on the supported chains and some getter functions.
- `localStorage.ts` file contains key contains used to keep the user options to localStorage.
- `tokens.ts` file contains information of the tokens used in the supported chains. It also provides some getter functions to get tokens easily.
- `ui.ts` file contains some constants used in UI components

#### domain
This folder contains helper functions used for specific purposes in the web app.

- `tokens` folder contains all types, hooks and utility functions related to the tokens in the platform.
- `useAdminActions.js` contains `useAdminActions` hook definition.
- `usePriceFeedTimelockMembership.js` contains `usePriceFeedTimelockMembership` hook definition.
- `useTimelockMembership.js` contains `useTimelockMembership` hook definition.

#### lib
This folder contains general helper functions used in components and pages.

- `chains` folder contains `useChainId` hook definition.
- `contracts` folder contains helper functions to fetch information from smart contracts and call smart contract methods (transactions).
- `localStorage` folder contains `useLocalStorageByChainId` and `useLocalStorageSerializeKey` hooks.
- `rpc` folder contains `getProvider` and `getFallbackProvider` helper functions.
- `subgraph` folder contains subgraph graphql client definitions and some helper functions to get the specified client easily.
- `wallets` folder contains `hooks` and helper functions related to wallet connection, network addition and switch.
- `dates.ts` contains the functions related to time and dates.
- `helperToast.ts` contains `helperToast` definition.
- `i18n.js` contains helper functions for translation.
- `legacy.js` contains hooks and helper functions from the old version of the platform.
- `number.ts` contains helper functions used to handle `BigNumber`s.
- `sleep.js` contains `sleep` helper function.
- `url.ts` contains `getRootUrl` helper function.
- `useLockBodyScroll.ts`, `usePrevious.js`, and `useScrollToTop.js` files contain `useLockBodyScroll`, `usePrevious`, and `useScrollToTop` hook definitions.

#### pages
This folder contains the web app page components.

- `FastPriceFeedSettings` page shows `fastPriceFeed` contract states and allow users to update them. Who can update the specific state is explained in each state tooltip.
- `PageNotFound` page is used when users enter undefined routes in the browser's address bar.
- `PriceFeedTimelockSettings` page shows `priceFeedTimelock` contract states and allow users to update them. Who can update the specific state is explained in each state tooltip.
- `TimelockSettings` page shows `timelock` contract states and allow users to update them. Who can update the specific state is explained in each state tooltip.
- `VaultPriceFeedSettings` page shows `vaultPriceFeed` contract states and allow users to update them. Who can update the specific state is explained in each state tooltip.
- `VaultSettings` page shows `vault` contract states and allow users to update them. Who can update the specific state is explained in each state tooltip.

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

4. create `/var/www/fxdx-admin` folder
```
sudo mkdir -p /var/www/fxdx-admin
sudo chown -R $USER:$USER /var/www/fxdx-admin
sudo chmod -R 755 /var/www/fxdx-admin
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

    root /var/www/fxdx-admin;
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
scp -i <path/to/the/key/file> -r <path/to/the/project>/build/* ubuntu@<server ip address>:/var/www/fxdx-admin
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
scp -i <path/to/the/key/file> -r <path/to/the/project>/build/* ubuntu@<server ip address>:/var/www/fxdx-admin
```
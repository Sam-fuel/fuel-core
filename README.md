# Fuel Core

[![npm version](https://badge.fury.io/js/fuel-core.svg)](https://badge.fury.io/js/fuel-core)

[![Gitter chat](https://badges.gitter.im/gitterHQ/gitter.png)](https://gitter.im/FuelLabs_/community)

Essential JavaScript architecture for the Fuel side chain.

## Features:
- A ***complete optimistic rollup side-chain*** implementation in JS
- ***Extremely cheap*** (under **3840 wei per transaction**)
- Pre-cleared instant transactions (**1.4 to .8 seconds on average**)
- Multi-network testnet support in **Goerli** and **Ropsten**
- **Meta-transactional** by default (pay fees in any ERC20)
- Complete node, faucet and wallet implementations
- ***Live pubsub support*** for instant balance updates
- ***Works with any ERC20 token*** (simply deposit to use)
- Supports **HTLC's for instant atomic swaps**
- ***High throughput capability*** (surge approx. 10k tps, on-chain 250tps)
- ***Censorship resistant*** (enter and exit at will)
- Completely open-source under **Apache-2.0**

## Install

```sh
npm install --save fuel-core
```

## Usage

```js
import { Wallet, utils } from "fuel-core";

const { faucet, transfer, listen, tokens, balance, address } = new Wallet({
   signer: new utils.SigningKey(utils.randomBytes(32)),
});

(async () => {
  await listen(async () => {
    console.log(await balance(tokens.fakeDai));
  });

  await faucet();

  await transfer(500, tokens.fakeDai, address);
})();
```

## Deposit / Withdraw

```js
import { Wallet, utils, providers, dbs } from "fuel-core";

const { deposit, balance, withdraw, retrieve, tokens } = new Wallet({
  signer: utils.SigningKey(utils.randomBytes(32)),
  provider: new providers.Web3Provider(window.web3.currentProvider),
  db: new dbs.Index(),
  chainId: 3, // default is 3 Ropsten (only supported)
});

(async ()=>{

  await deposit(1000, tokens.ether /*, { from: eth_accounts[0] } */);

  console.log(await balance(tokens.ether));

  await withdraw(500, tokens.ether); // make a withdrawal UTXO

  await retrieve(tokens.ether); // wait 1 weeks, select withdrawal zero or first withdrawal in DB to retrieve [, withdrawlIndex]

})()

```

## Listener Support

```js
const { listen } = new Wallet(...);

(async ()=>{

  // a cb can be added here, results are { key: , value: } objects.
  // fires for all wallet events and incoming transactions
  await listen(/* callback */);

})();
```

## Syncing

```js
const { sync } = new Wallet(...);

// Sync your wallet DB with the Fuel API
(async ()=>{

  await sync();

})()

```

## Wallet

```js
new Wallet({
  signer, // [Object] ethers Signer object
  provider, // [Object] web3 provider object
  db: new dbs.Memory(), // [Object] database object
  chainId: '3' // [String | Number] '3' Ropsten or '5' Goerli (only supported)
  // alternative: [String] network: 'ropsten' // or 'goerli'
});
```

### Databases

```js
const { dbs } = require('fuel-core');

new dbs.Index(...); // Based on window.localStorage
new dbs.Level({ ... }); // Level store (level)
new dbs.Memory({ ... }); // Memory storage (memdown)
```

## Running a Node

```sh
git clone https://github.com/fuellabs/fuel-core
# setup your environment variables (described below)
npm install
# then run one of the following commands
```

- Full Sync - `npm run node-sync-ropsten` runs a full node with mempool optionality with remote MySQL DB
- Faucet - `npm run node-faucet-ropsten` runs a faucet node (for fund requests/dispersal of test tokens)
- Verifier `npm run node-verifier-ropsten` runs a verifier node (no mempool or block production)
- Gancache `npm run node-ganache` runs a local Ganache server

Note, Goerli is also supported, i.e. `npm run node-sync-goerli` etc.

## Building and Deploying

- Build Fuel `npm run build-fuel` builds the Fuel.sol contracts, writes artifacts
- Deploy Fuel `npm run deploy-fuel-ropsten` deploys Fuel based on environmental configuration (`npm run deploy-fuel-goerli` also supported)

## Running a Test MySQL Sync Node

1) Fill out environment variables.
2) One instance:
```sh
npm run node-ganache
```
3) Another instance:
```sh
npm run test-node-sync
```

### Environment Variables

Set these in your `~/.bashrc` or `~/.bash_profile` to operate the sync and dispersal nodes.

```sh
# Web3 HTTP Provider (string - url.. Infura or Other *Required*)
ropsten_web3_provider="https://.."
# goerli_web3_provider="https://.."

# MySQL Database Environment (string[s] - mysql connection settings, optional for sync or verifier nodes)
# ropsten_mysql_host
# ropsten_mysql_port
# ropsten_mysql_database
# ropsten_mysql_user
# ropsten_mysql_password

# goerli_mysql_host
# goerli_mysql_port
# goerli_mysql_database
# goerli_mysql_user
# goerli_mysql_password

# Keys (string - 64 byte hex)
# block_production_key=0x..
# fraud_commitment_key=0x..
# transactions_submission_keys=0x..[, 0x...]

# Faucet (string - hex)
# faucet_key=0x..

# Faucet (numberish - hex | number)
# faucet_token_id=1
# faucet_dispersal_amount=0x..

# Sentry Error Reporting (optional)
# sentry=https://..

# Monitor Memory (boolish, optional)
# memwatch=1

# Verifier Only (no block production, only fraud commitments etc..) (boolish, optional)
# verifier=1

# Chain Identifier (number, eventually 1 Mainnet or 3 Ropsten are supported, default: 3)
# chain_id=3

# Gas Limit (string - hex)
# gasLimit=0x..

# PubNub
# pubnub_publisher_key=
# pubnub_subscriber_key=
```

## Help out

There is always a lot of work to do, and will have many rules to maintain. So please help out in any way that you can:

- Create, enhance, and debug fuel-core rules (see our guide to ["Working on rules"](./.github/CONTRIBUTING.md)).
- Improve documentation.
- Chime in on any open issue or pull request.
- Open new issues about your ideas for making `fuel-core` better, and pull requests to show us how your idea works.
- Add new tests to *absolutely anything*.
- Create or contribute to ecosystem tools.
- Spread the word!

We communicate via [issues](https://github.com/fuellabs/fuel-core/issues) and [pull requests](https://github.com/fuellabs/fuel-core/pulls).

## Important documents

- [Changelog](CHANGE_LOG.md)
- [License](https://raw.githubusercontent.com/fuellabs/fuel-core/master/LICENSE)

## Donate

Please consider donating if you think Fuel is helpful to you or that my work is valuable. I am happy if you can help us buy a cup of coffee. ❤️

- [Gitcoin](https://gitcoin.co/grants/199/fuel-labs)

Or just send us some *Dai*, *USDC* or *Ether*:

**0x3e947a271a37Ae7B59921c57be0a3246Ee0d887C** [Etherscan](https://etherscan.io/address/0x3e947a271a37Ae7B59921c57be0a3246Ee0d887C)

## Licence

This project is licensed under the Apache-2.0 license, Copyright (c) 2020 Fuel labs. For more information see LICENSE

```
Copyright 2020 Fuel Labs

Licensed under the Apache License, Version 2.0 (the "License");
you may not use this file except in compliance with the License.
You may obtain a copy of the License at

    http://www.apache.org/licenses/LICENSE-2.0

Unless required by applicable law or agreed to in writing, software
distributed under the License is distributed on an "AS IS" BASIS,
WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
See the License for the specific language governing permissions and
limitations under the License.
```

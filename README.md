Gnosis Safe Contracts
=====================

[![npm version](https://badge.fury.io/js/%40gnosis.pm%2Fsafe-contracts.svg)](https://badge.fury.io/js/%40gnosis.pm%2Fsafe-contracts)
[![Build Status](https://travis-ci.org/gnosis/safe-contracts.svg?branch=development)](https://travis-ci.org/gnosis/safe-contracts)

Install
-------
### Install requirements with npm:

```bash
npm install
```

### Run all tests (requires Node version >=7 for `async/await`):

```bash
npx truffle compile
npx truffle test
```

### Deploy

Some contracts require that the Solidity compile target is `petersburg` (e.g. ProxyFactory and MultiSend). This is default since [Solidity 0.5.5](https://github.com/ethereum/solidity/releases/tag/v0.5.5).

Note: The formal verification was performed using the contract compiled with solcjs 0.5.0.

Preparation:
```bash
export MNEMONIC="<mnemonic>"
```

zOS:
- Make sure that zos is version 2
- Make sure that all dependencies use solcjs >0.5.0
- Add `txParams['from'] = txParams['from'] || web3.currentProvider.getAddress(0)` in `Transactions.js` of the `zos-lib` module
```bash
virtualenv env -p python3
. env/bin/activate
python ./scripts/deploy_safe_contracts_zos.py
```

Truffle:

```bash
npx truffle deploy
```

Verify Contracts:
- requires installed solc (>0.5.0)
```bash
virtualenv env -p python3
. env/bin/activate
pip install solidity-flattener
mkdir build/flattened_contracts
solidity_flattener contracts/GnosisSafe.sol --output build/flattened_contracts/GnosisSafe.sol
solidity_flattener contracts/libraries/CreateAndAddModules.sol --output build/flattened_contracts/CreateAndAddModules.sol --solc-paths="/=/"
solidity_flattener contracts/libraries/MultiSend.sol --output build/flattened_contracts/MultiSend.sol --solc-paths="/=/"
solidity_flattener contracts/modules/DailyLimitModule.sol --output build/flattened_contracts/DailyLimitModule.sol --solc-paths="/=/"
solidity_flattener contracts/modules/SocialRecoveryModule.sol --output build/flattened_contracts/SocialRecoveryModule.sol --solc-paths="/=/"
solidity_flattener contracts/modules/StateChannelModule.sol --output build/flattened_contracts/StateChannelModule.sol --solc-paths="/=/"
solidity_flattener contracts/modules/WhitelistModule.sol --output build/flattened_contracts/WhitelistModule.sol --solc-paths="/=/"
solidity_flattener contracts/proxies/ProxyFactory.sol --output build/flattened_contracts/ProxyFactory.sol
find build/flattened_contracts -name '*.sol' -exec sed -i '' 's/pragma solidity ^0.4.13;/pragma solidity ^0.5.0;/g' {} \;
```

Using with ZeppelinOS
---------------------

You can create a gnosis safe upgradeable instance using [ZeppelinOS](http://zeppelinos.org/) by linking to the provided [EVM package](https://docs.zeppelinos.org/docs/linking.html). This will use the master copy already deployed to mainnet, kovan, or rinkeby, reducing gas deployment costs. 

To create an instance using ZeppelinOS:

```bash
$ npm install -g zos
$ zos init YourProject
$ zos link gnosis-safe
$ zos push --network rinkeby
> Connecting to dependency gnosis-safe 0.1.0
$ zos create gnosis-safe/GnosisSafe --init setup --args "[$ADDRESS1,$ADDRESS2,$ADDRESS3],2,0x0000000000000000000000000000000000000000,\"\"" --network rinkeby --from $SENDER
> Instance created at SAFE_ADDRESS
```

It is suggested to [use a non-default address](https://docs.zeppelinos.org/docs/pattern.html#transparent-proxies-and-function-clashes) as `$SENDER`.

> Note: When using the contracts via ZeppelinOS make sure to choose an appropriate Proxy admin. An upgradable proxy enables the user to update the master copy (aka implementation). The default upgradable proxy is managed by an admin address. This admin address is independent from the owners of the Safe. Therefore it would be possible for the admin to change the master copy without the approval of any owner, thus allowing him to gain full access to the Safe.

Documentation
-------------
- [ReadTheDocs](http://gnosis-safe.readthedocs.io/en/latest/)
- [Coding guidlines](docs/guidelines.md)

Audits/ Formal Verification
---------
- [by Runtime Verification](docs/rv_1_0_0.md)
- [by Alexey Akhunov](docs/alexey_audit.md)

Security and Liability
----------------------
All contracts are WITHOUT ANY WARRANTY; without even the implied warranty of MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.

License
-------
All smart contracts are released under LGPL v.3.

Contributors
------------
- Stefan George ([Georgi87](https://github.com/Georgi87))
- Richard Meissner ([rmeissner](https://github.com/rmeissner))
- Christian Lundkvist ([christianlundkvist](https://github.com/christianlundkvist))
- Nick Dodson ([SilentCicero](https://github.com/SilentCicero))
- Gonçalo Sá ([GNSPS](https://github.com/GNSPS))

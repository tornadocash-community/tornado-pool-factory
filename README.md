# Tornado Instances Factory

## About

This repository contains:

1. `InstanceFactory` - instance factory for the creation new Tornado ERC20/native pools
2. `InstanceProposalCreator` - governance proposal factory for the addition of new Tornado instances to the Tornado router

### InstanceFactory

Anyone can create a new instance by calling `createInstanceClone` method of the factory with parameters:

1. `address token` - address of ERC20 token for a new instance, zero address for the native instance
2. `uint256 denomination` - denomination for new instance (tokens can only be deposited in certain denominations into instances)

### InstanceProposalCreator

Anyone can create governance proposal for the addition of a new instance by calling `createProposalApprove/createProposalPermit` method of the factory with parameters (proposal creation fee in TORN is charged from sender):

1. `address token` - address of ERC20 token for a new instance, zero address for the native instance
2. `uint24 uniswapPoolSwappingFee` - fee value of Uniswap instance which will be used for `TORN/token` price determination. `3000` means 0.3% fee Uniswap pool. Zero value for the native instance.
3. `uint256[] denominations` - list of denominations for each new instance (tokens can only be deposited in certain denominations into instances).
4. `uint32[] protocolFees` - list of protocol fees for each new instance (this fee is only charged from registrated relayer during withdrawal process). `100` means 1% of instance denomination fee for withdrawal throw registrated relayer.

## Factory parameters

### InstanceProposalCreator

1. `max number of new instances in one proposal` - the current version supports the addition of a maximum of 4 instances at once.
2. `proposal creation fee` - this fee is charged from creator of proposal during `createProposalApprove/createProposalPermit` factory method execution. It can be changed by governance. Default value is stored in `config.js`.
3. `TWAPSlotsMin` - minimum number of TWAP slots for Uniswap pool that is chosen during `createProposalApprove/createProposalPermit` factory method call. It can be changed by governance. Default value is stored in `config.js`.

## Warnings

1. This version of the factory creates a proposal for **immutable** Tornado instance initialization.
2. For `InstanceProposalCreator` users should manually propose a proposal after its creation using the factory (in governance UI for example). As `propose()` method caller must have 1000 TORN locked in the governance. Moreover, the proposer can't propose more than one proposal simultaneously.

## Tests

Setting up the repository:

```bash
    yarn
    cp .env.example .env
```

Please fill out .env according to the template provided in it. Please ensure that all of the example values are set to the correct addresses.

To run test scripts:

```bash
    yarn test
```

Test scripts cover instance factory deployment, proposal deployment and executing proposal.

## Deploy

Check config.js for actual values.

With `salt` = `0x0000000000000000000000000000000000000000000000000000000047941987` address must be:

1. `SidechainInstanceFactory` - `0x3D36D4b204E68e8A1431ec31Bea1fFbA3eC04590`
2. `SidechainInstanceFactory proxy` - `0x9d5C91ccF5c58b312896C4B4De45426fA128b001`
3. `InstanceFactory` - `0x83216783423500116723884F70dB44fB7BBb855b`
4. `InstanceFactory proxy` - `0x300aF836C74c1Af73f4353130AC1314Ed59B6a0A`
5. `InstanceProposalCreator` - `0x2b73b3555F1904CB8042cA6703734438eD4F28a0`
6. `InstanceProposalCreator proxy` - `0x84E013c8699Cb115906929B68b1Dc5ca5B2Fc46c`

Check addresses with current config:

```shell
    yarn compile
    node -e 'require("./src/generateAddresses").generateWithLog()'
```

Deploy SidechainInstanceFactory:

Check config.js for actual **admin** value for this sidechain (TornadoCash community multisig).

```shell
    yarn hardhat run scripts/deploySidechainInstanceFactory.js --network mainnet
```

Deploy InstanceProposalCreator:

```shell
    yarn hardhat run scripts/deployInstanceProposalCreator.js --network mainnet
```

Verify on Etherscan:

```
    yarn hardhat verify --network <network-name> <contract-address> <constructor-arguments>
```

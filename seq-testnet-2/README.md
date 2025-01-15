# seq-testnet-2

- [Network Details](#network-details)
- [Run the Sequencer](#run-the-sequencer)
- [Monitoring](#monitoring)

## Network Details

- **Chain ID**: `seq-testnet-2`
- **Launch date**: 2024-12-04
- **Current binary version**: `seq-testnet-2` or `seq-testnet-2-improved-sidecar`
- **Launch binary version**: `seq-testnet-2`
- **Genesis file**: [genesis.json in this folder](./genesis.json).
- **Minimum gas price**: `10test`

Useful links:

- **Explorer**: https://fuel-seq.simplystaking.xyz/fuel-testnet
- **Public RPC**: https://testnet-rpc-fuel-seq.simplystaking.xyz
- **Public REST**: https://testnet-rest-fuel-seq.simplystaking.xyz
- **Public gRPC**: testnet-grpc-fuel-seq.simplystaking.xyz:443
- **API docs**: https://testnet-rest-fuel-seq.simplystaking.xyz/swagger/
- **Indexer**: http://testnet-indexer-fuel-seq.simplystaking.xyz

Alternate public nodes:

- **Public RPC**: https://fuel-testnet-rpc.polkachu.com

For a list of historical Sequencer upgrades, refer to the [upgrades doc](./UPGRADES.md).

## Run the Sequencer

> **NOTE**: the guides assume some knowledge around running Cosmos SDK based nodes. It is highly recommended to familiarise yourself with this type of node operation before running a Fuel Sequencer node.

There are two main options for running the Sequencer:

- Run a Sequencer Full Node (non-Validator).
- Run a Sequencer Validator, which requires you to run a Sidecar and an Ethereum node as well.

The first steps for both cases are the same, and can be found in the [Run Sequencer Node](./RUN_NODE.md) document. Afterward, to continue the steps towards running a validator, refer to the [Run Sequencer Validator](./RUN_VALIDATOR.md) document.

## Monitoring

Refer to the [monitoring guide](./MONITORING.md) for steps on how to monitor your setup.

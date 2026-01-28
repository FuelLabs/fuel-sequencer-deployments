# seq-testnet-2

- [Network Details](#network-details)
- [Run the Sequencer](#run-the-sequencer)
- [Monitoring](#monitoring)

## Network Details

- **Chain ID**: `seq-testnet-2`
- **Launch date**: 2024-12-04
- **Current binary version**: `seq-testnet-2.6`
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

For a list of historical Sequencer upgrades, refer to the [upgrades doc](./UPGRADES.md).

## Run the Sequencer

> **NOTE**: the guides assume some knowledge around running Cosmos SDK based nodes. It is highly recommended to familiarise yourself with this type of node operation before running a Fuel Sequencer node.

These are the main options for running the Sequencer:

- [Run a Sequencer Full Node (non-Validator)](./RUN_NODE.md)
- [Run a Sequencer Validator, which requires you to run a Sidecar and an Ethereum node as well](./RUN_VALIDATOR.md).

> **IMPORTANT**: If you are running a validator in a sentry node architecture, you should follow the validator guide when setting up the sentry nodes as well. This is especially because your sentry nodes should be connected to a Sidecar as well. Otherwise, your validator's performance might suffer.

## Monitoring

Refer to the [monitoring guide](./MONITORING.md) for steps on how to monitor your setup.

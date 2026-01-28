# seq-mainnet-1

- [Network Details](#network-details)
- [Run the Sequencer](#run-the-sequencer)
- [Monitoring](#monitoring)

## Network Details

- **Chain ID**: `seq-mainnet-1`
- **Launch date**: 2024-11-21
- **Current binary version**: `seq-mainnet-1.7`
- **Previous binary version**: `seq-mainnet-1.5`, `seq-mainnet-1.6`
- **Launch binary version**: `seq-mainnet-1.1`
- **Genesis file**: [genesis.json in this folder](./genesis.json).
- **Minimum gas price**: `10fuel`

Useful links:

- **Explorer**: https://fuel-seq.simplystaking.xyz/fuel-mainnet
- **Public RPC**: https://rpc-fuel-seq.simplystaking.xyz
- **Public REST**: https://rest-fuel-seq.simplystaking.xyz
- **Public gRPC**: grpc-fuel-seq.simplystaking.xyz:443
- **API docs**: https://rest-fuel-seq.simplystaking.xyz/swagger/
- **Indexer**: https://indexer-fuel-seq.simplystaking.xyz

Alternate public nodes and services:

- **Public RPC**: https://fuel-rpc.polkachu.com
- **Public RPC**: https://rpc-fuel.kintsugi-nodes.com
- **Public RPC**: https://rpc.fuel.cumulo.com.es
- **Services**: https://itrocket.net/services/mainnet/fuel/

For a list of historical Sequencer upgrades, refer to the [upgrades doc](./UPGRADES.md).

## Run the Sequencer

> **NOTE**: the guides assume some knowledge around running Cosmos SDK based nodes. It is highly recommended to familiarise yourself with this type of node operation before running a Fuel Sequencer node.

These are the main options for running the Sequencer:

- [Run a Sequencer Full Node (non-Validator)](./RUN_NODE.md)
- [Run a Sequencer Validator, which requires you to run a Sidecar and an Ethereum node as well](./RUN_VALIDATOR.md).

> **IMPORTANT**: If you are running a validator in a sentry node architecture, you should follow the validator guide when setting up the sentry nodes as well. This is especially because your sentry nodes should be connected to a Sidecar as well. Otherwise, your validator's performance might suffer.

## Monitoring

Refer to the [monitoring guide](./MONITORING.md) for steps on how to monitor your setup.

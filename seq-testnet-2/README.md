# seq-testnet-2

- [Network Details](#network-details)
- [Run the Sequencer](#run-the-sequencer)
- [Monitoring](#monitoring)

## Network Details

- **Chain ID**: `seq-testnet-2`
- **Launch date**: 2024-12-04
- **Current binary version**: `seq-testnet-2`
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

There are two main options for running a Sequencer node:

- Run a Sequencer Full Node (non-Validator).
- Run a Sequencer Validator with Sidecar, which requires a connection to an Ethereum node.

The first steps for both cases are the same, and can be found in the [Run a Sequencer Node](./RUN_NODE.md) document.

## Monitoring

Refer to the [monitoring guide](./MONITORING.md) for steps on how to monitor your setup.

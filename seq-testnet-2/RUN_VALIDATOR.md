# Run Sequencer Validator

- [Typical Setup](#typical-setup)
- [Prerequisites](#prerequisites)
- [Run an Ethereum Sepolia Node](#run-an-ethereum-sepolia-full-node)
- [Further Sequencer Configurations](#further-sequencer-configurations)
- [Run a Sidecar](#run-a-sidecar)
- [Creating an Account](#creating-an-account)
- [Create the Validator](#create-the-validator)
  - [What to Expect](#what-to-expect)
  - [Tendermint KMS](#tendermint-kms)
  - [Additional Advanced Configuration](#additional-advanced-configuration)

## Typical Setup

The validator setup will consist of a Fuel Sequencer, Sidecar, and a connection to an Ethereum Sepolia Node.

![setup](Setup.drawio.png)

## Prerequisites

This guide assumes that you've gone through the steps to [run a Sequencer Node](./RUN_NODE.md).

Unless otherwise configured, at least the following ports should be available:

- **Sequencer**: 26656, 26657, 9090, 1317
- **Sidecar**: 8080
- **Ethereum**: 8545, 8546

These components communicate together, so any reconfiguration of the above ports likely needs to be reflected on the other respective component as well. Most notably:

- Changes to the **Sequencer ports** need to be reflected in the **Sidecar's runtime flags**.
- Changes to the **Sidecar port** need to be reflected in the **Sequencer's app config**.
- Changes to the **Ethereum ports** need to be reflected in the **Sidecar's runtime flags**.

The minimum requirements for the **Sequencer** and **Sidecar** together are:
 
- 4 cores
- 8 GB ram
- 200GB disk space

## Run an Ethereum Sepolia Full Node

To ensure the highest performance and reliability of the Sequencer infrastructure, **running your own Ethereum Sepolia full node is a requirement**. Avoiding the use of third-party services for Ethereum node operations significantly helps the Sequencer network's liveness. Please note these recommended node configurations:

```
--syncmode=snap
--gcmode=full
```

## Further Sequencer Configurations

Configure the node (`~/.fuelsequencer/config/app.toml`):

- Set `minimum-gas-prices = "10test"`.
- Configure `[sidecar]`:
  - Ensure that `enabled = true`.
  - Ensure that `address` is where the Sidecar will run.
- Configure `[api]`:
  - Set `swagger=true` (optional).
  - Set `rpc-max-body-bytes = 1153434` (optional - relevant for public REST).
- Configure `[commitments]`:
  - Set `api-enabled = true` (optional - relevant for public REST).
- Configure `[state-sync]`:
  - Set `snapshot-interval = 1000` (optional - to provide state-sync service).
- Configure:
  - Set `rpc-read-timeout = 10` (optional - relevant for public REST).
  - Set `rpc-write-timeout = 0` (optional - relevant for public REST).

> **WARNING**: leaving the `[commitments]` API accessible to anyone can lead to DoS! It is highly recommended to handle whitelisting or authentication by a reverse proxy like [Traefik](https://traefik.io/traefik/) for gRPC if the commitments API is enabled.

## Run a Sidecar

Here's an example service file with some placeholder (`<...>`) values for the **Sidecar**:

```sh
[Unit]
Description=FuelSequencer Sidecar
After=network.target

[Service]
Type=simple
User=<USER>
ExecStart=<HOME>/go/bin/fuelsequencerd start-sidecar \
    --host "0.0.0.0" \
    --sequencer_grpc_url "127.0.0.1:9090" \
    --eth_ws_url "<ETHEREUM_NODE_WS>" \
    --eth_rpc_url "<ETHEREUM_NODE_RPC>" \
    --eth_contract_address "0x0E5CAcD6899a1E2a4B4E6e0c8a1eA7feAD3E25eD"
Restart=on-failure
RestartSec=3
LimitNOFILE=4096

[Install]
WantedBy=multi-user.target
```

It is specifically very important to ensure that you provide all the necessary flags when running the Sidecar to ensure that it can connect to an Ethereum node and to the Sequencer node, and is also accessible by the Sequencer node. The most important flags are:

- `host`: host for the gRPC server to listen on
- `port`: port for the gRPC server to listen on
- `eth_ws_url`: Ethereum node WebSocket endpoint
- `eth_rpc_url`: Ethereum node RPC endpoint
- `eth_contract_address`: address in hex format of the contract to monitor for logs
- `sequencer_grpc_url`: Sequencer node gRPC endpoint

## Creating an Account

Generate an address with a key name:

```sh
fuelsequencerd keys add <NAME> # for a brand new key

# or

fuelsequencerd keys add <NAME> --recover # to create from a mnemonic
```

This will give you an output with an address (e.g. `fuelsequencer1l7qk9umswg65av0zygyymgx5yg0fx4g0dpp2tl`) and a private mnemonic, if you generated a brand new key. Store the mnemonic safely.

Fuel Sequencer addresses also have an Ethereum-compatible (i.e. hex) format. To generate the hex address corresponding to your Sequencer address, run the following:

```sh
fuelsequencerd keys parse <ADDRESS>
```

This will give an output in this form:

```text
bytes: FF8162F37072354EB1E222084DA0D4221E93550F
human: fuelsequencer
```

Adding the `0x` prefix to the address in the first line gives you your Ethereum-compatible address, used to deposit into and interact iwht your Sequencer address from Ethereum.

> **WARNING**: always test transfer small amounts first if you are going to bridge FUEL tokens to this Ethereum-compatible address.

## Create the Validator

To create the validator, run:

```sh
fuel-sequencerd tx staking create-validator path/to/validator.json \
    --from keyname \
    --gas auto \
    --gas-prices 10fuel \
    --gas-adjustment 1.5 \
    --chain-id seq-testnet-2
```

...where validator.json contains:

```json
{
	"pubkey": {"@type":"/cosmos.crypto.ed25519.PubKey","key":"<PUBKEY>"},
	"amount": "1000000000fuel",
	"moniker": "<MONIKER>",
	"identity": "<OPTIONAL-IDENTITY>",
	"website": "<OPTIONAL-WEBSITE>",
	"security": "<OPTIONAL-EMAIL>",
	"details": "<OPTIONAL-DETAILS>",
	"commission-rate": "0.05",
	"commission-max-rate": "<MAX-RATE>",
	"commission-max-change-rate": "<MAX-CHANGE-RATE>",
	"min-self-delegation": "1"
}
```

...where the pubkey can be obtained using `fuelsequencerd tendermint show-validator`.

### What to Expect

The Sequencer should show block generation, and the Sidecar should show block extraction.

### Tendermint KMS

If you will be using `tmkms`, make sure that in the config:

- Chain ID is set to `seq-testnet-2` wherever applicable
- `account_key_prefix = "fuelsequencerpub"`
- `consensus_key_prefix = "fuelsequencervalconspub"`
- `sign_extensions = true`
- `protocol_version = "v0.34"`

### Additional Advanced Configuration

Sidecar flags:

- `development`: starts the sidecar in development mode.
- `eth_max_block_range`: max number of Ethereum blocks queried at one go.
- `eth_min_logs_query_interval`: minimum wait between successive queries for logs.
- `unsafe_eth_start_block`: the Ethereum block to start querying from.
- `unsafe_eth_end_block`: the last Ethereum block to query. Incorrect use can cause the validator to propose empty blocks, leading to slashing!
- `sequencer_path_to_cert_file`: path to the certificate file of the Sequencer infrastructure for secure communication. Specify this value if the Sequencer infrastructure was set up using TLS.
- `sidecar_path_to_cert_file`: path to the certificate file of the sidecar server for secure communication. Specify this value if you want to set up a sidecar server with TLS.
- `sidecar_path_to_key_file`: path to the private key file of the sidecar server for secure communication. Specify this value if you want to set up a sidecar server with TLS.
- `prometheus_enabled`: enables serving of prometheus metrics.
- `prometheus_listen_address`: address to listen for prometheus collectors (default ":8081").
- `prometheus_max_open_connections`: max number of simultaneous connections (default 3).
- `prometheus_namespace`: instrumentation namespace (default "sidecar").
- `prometheus_read_header_timeout`: amount of time allowed to read request headers (default 10s).
- `prometheus_write_timeout`: maximum duration before timing out writes of the response (default 10s).

Sidecar client flags:

- `sidecar_grpc_url`: the sidecar's gRPC endpoint.
- `query_timeout`: how long to wait before the request times out.

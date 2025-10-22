# Run Sequencer Validator

- [Typical Setup](#typical-setup)
- [Prerequisites](#prerequisites)
- [Run an Ethereum Mainnet Full Node](#run-an-ethereum-mainnet-full-node)
- [Configure the Sequencer](#configure-the-sequencer)
  - [Install Cosmovisor](#install-cosmovisor)
  - [Configure State Sync](#configure-state-sync)
- [Run the Sidecar](#run-the-sidecar)
  - [Linux](#linux)
  - [Mac](#mac)
- [Run the Sequencer](#run-the-sequencer)
  - [Linux](#linux-1)
  - [Mac](#mac-1)
- [Creating an Account](#creating-an-account)
- [Create the Validator](#create-the-validator)
  - [What to Expect](#what-to-expect)
  - [Tendermint KMS](#tendermint-kms)
  - [Additional Advanced Configuration](#additional-advanced-configuration)
- [References](#references)

## Typical Setup

The validator setup will consist of a Fuel Sequencer, Sidecar, and a connection to an Ethereum Mainnet Node.

![setup](Setup.drawio.png)

## Prerequisites

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

The guide assumes that Golang is installed in order to run Cosmovisor. We recommend installing version `1.21+`.

## Run an Ethereum Mainnet Full Node

To ensure the highest performance and reliability of the Sequencer infrastructure, **running your own Ethereum Mainnet full node is a requirement**. Avoiding the use of third-party services for Ethereum node operations significantly helps the Sequencer network's liveness. Please note these recommended node configurations:

```
--syncmode=snap
--gcmode=full
```

## Configure the Sequencer

Obtain binary and genesis from this repository:

- Binary from: https://github.com/FuelLabs/fuel-sequencer-deployments/releases/tag/seq-mainnet-1.5
- Genesis from: https://github.com/FuelLabs/fuel-sequencer-deployments/blob/main/seq-mainnet-1/genesis.json

Download the right binary based on your architecture to `$GOPATH/bin/` with the name `fuelsequencerd`:

- `echo $GOPATH` to ensure it exists. If not, `go` might not be installed.
- `mkdir $GOPATH/bin/` if the directory does not exist.
- `wget <url/to/binary>` to download the binary, or any equivalent approach.
- `cp <binary> $GOPATH/bin/fuelsequencerd`

Try the binary:

```sh
fuelsequencerd version  # expect seq-mainnet-1.5
```

Initialise the node directory, giving your node a meaningful name:

```sh
fuelsequencerd init <node-name> --chain-id seq-mainnet-1
```

Copy the downloaded genesis file to `~/.fuelsequencer/config/genesis.json`:

```sh
cp <path/to/genesis.json> ~/.fuelsequencer/config/genesis.json
```

Configure the node (part 1: `~/.fuelsequencer/config/app.toml`):

- Set `minimum-gas-prices = "10fuel"`.
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

Configure the node (part 2: `~/.fuelsequencer/config/config.toml`):

- Configure `[p2p]`:
  - Set `persistent_peers = "fc5fd264190e4a78612ec589994646268b81f14e@80.64.208.207:26656"`.
- Configure `[mempool]`:
  - Set `max_tx_bytes = 1258291` (1.2MiB)
  - Set `max_txs_bytes = 23068672` (22MiB)
- Configure `[rpc]`:
  - Set `max_body_bytes = 1153434` (optional - relevant for public RPC).

> Note: Ensuring consistent CometBFT mempool parameters across all network nodes is important to reduce transaction delays. This includes `mempool.size`, `mempool.max_txs_bytes`, and `mempool.max_tx_bytes` in [config.toml](https://docs.cometbft.com/v0.38/core/configuration) and `minimum-gas-prices` in [app.toml](https://docs.cosmos.network/main/learn/advanced/config), as pointed out above.

### Install Cosmovisor

To install Cosmovisor, run `go install cosmossdk.io/tools/cosmovisor/cmd/cosmovisor@latest`

Set the environment variables:

<details>
  <summary>If you're running on a zsh terminal...</summary>

  ```zsh
  echo "# Setup Cosmovisor" >> ~/.zshrc
  echo "export DAEMON_NAME=fuelsequencerd" >> ~/.zshrc
  echo "export DAEMON_HOME=$HOME/.fuelsequencer" >> ~/.zshrc
  echo "export DAEMON_ALLOW_DOWNLOAD_BINARIES=true" >> ~/.zshrc
  echo "export DAEMON_LOG_BUFFER_SIZE=512" >> ~/.zshrc
  echo "export DAEMON_RESTART_AFTER_UPGRADE=true" >> ~/.zshrc
  echo "export UNSAFE_SKIP_BACKUP=true" >> ~/.zshrc
  echo "export DAEMON_SHUTDOWN_GRACE=15s" >> ~/.zshrc
  
  # You can check https://docs.cosmos.network/main/tooling/cosmovisor for more configuration options.
  ```

  Apply to your current session: `source ~/.zshrc`
</details>

<details>
  <summary>If you're running on a bash terminal...</summary>

  ```zsh
  echo "# Setup Cosmovisor" >> ~/.bashrc
  echo "export DAEMON_NAME=fuelsequencerd" >> ~/.bashrc
  echo "export DAEMON_HOME=$HOME/.fuelsequencer" >> ~/.bashrc
  echo "export DAEMON_ALLOW_DOWNLOAD_BINARIES=true" >> ~/.bashrc
  echo "export DAEMON_LOG_BUFFER_SIZE=512" >> ~/.bashrc
  echo "export DAEMON_RESTART_AFTER_UPGRADE=true" >> ~/.bashrc
  echo "export UNSAFE_SKIP_BACKUP=true" >> ~/.bashrc
  echo "export DAEMON_SHUTDOWN_GRACE=15s" >> ~/.bashrc
  
  # You can check https://docs.cosmos.network/main/tooling/cosmovisor for more configuration options.
  ```

  Apply to your current session: `source ~/.bashrc`
</details>

You can now test that cosmovisor was installed properly:

```sh
cosmovisor version
```

Initialise Cosmovisor directories (hint: `whereis fuelsequencerd` for the path):

```sh
cosmovisor init <path/to/fuelsequencerd>
```

At this point `cosmovisor run` will be the equivalent of running `fuelsequencerd`, however you should _not_ run the node for now.

### Configure State Sync

State Sync allows a node to get synced up quickly.

To configure State Sync, you will need to set these values in `~/.fuelsequencer/config/config.toml` under `[statesync]`:

- `enable = true` to enable State Sync
- `rpc_servers = ...`
- `trust_height = ...`
- `trust_hash = ...`

The last three values can be obtained from [the explorer](https://fuel-seq.simplystaking.xyz/fuel-mainnet/statesync).

You will need to specify at least two comma-separated RPC servers in `rpc_servers`. You can either refer to the list of alternate RPC servers above or use the same one twice.

## Run the Sidecar

At this point you should already be able to run `fuelsequencerd start-sidecar` with the right flags, to run the Sidecar. However, **it is highly recommended to run the Sidecar as a background service**.

It is also very important to ensure that you provide all the necessary flags when running the Sidecar to ensure that it can connect to an Ethereum node and to the Sequencer node, and is also accessible by the Sequencer node. The most important flags are:

- `host`: host for the gRPC server to listen on
- `port`: port for the gRPC server to listen on
- `eth_ws_url`: Ethereum node WebSocket endpoint
- `eth_rpc_url`: Ethereum node RPC endpoint
- `eth_contract_address`: address in hex format of the contract to monitor for logs. This MUST be set to `0xBa0e6bF94580D49B5Aaaa54279198D424B23eCC3`.
- `sequencer_grpc_url`: Sequencer node gRPC endpoint

### Linux

On Linux, you can use `systemd` to run the Sequencer in the background. Knowledge of how to use `systemd` is assumed here.

Here's an example service file with some placeholder (`<...>`) values that must be filled-in:

<details>
  <summary>Click me...</summary>

```sh
[Unit]
Description=Sidecar
After=network.target

[Service]
Type=simple
User=<USER>
ExecStart=<HOME>/go/bin/fuelsequencerd start-sidecar \
    --host "0.0.0.0" \
    --sequencer_grpc_url "127.0.0.1:9090" \
    --eth_ws_url "<ETHEREUM_NODE_WS>" \
    --eth_rpc_url "<ETHEREUM_NODE_RPC>" \
    --eth_contract_address "0xBa0e6bF94580D49B5Aaaa54279198D424B23eCC3"
Restart=on-failure
RestartSec=3
LimitNOFILE=4096

[Install]
WantedBy=multi-user.target
```
</details>

### Mac

On Mac, you can use `launchd` to run the Sequencer in the background. Knowledge of how to use `launchd` is assumed here.

Here's an example plist file with some placeholder (`[...]`) values that must be filled-in:

<details>
  <summary>Click me...</summary>

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
<dict>
    <key>Label</key>
    <string>fuel.sidecar</string>

    <key>ProgramArguments</key>
    <array>
        <string>/Users/[User]/go/bin/fuelsequencerd</string>
        <string>start-sidecar</string>
        <string>--host</string>
        <string>0.0.0.0</string>
        <string>--sequencer_grpc_url</string>
        <string>127.0.0.1:9090</string>
        <string>--eth_ws_url</string>
        <string>[ETHEREUM_NODE_WS]</string>
        <string>--eth_rpc_url</string>
        <string>[ETHEREUM_NODE_RPC]</string>
        <string>--eth_contract_address</string>
        <string>0xBa0e6bF94580D49B5Aaaa54279198D424B23eCC3</string>
    </array>

    <key>UserName</key>
    <string>[User]</string>

    <key>KeepAlive</key>
    <dict>
        <key>SuccessfulExit</key>
        <false/>
    </dict>

    <key>HardResourceLimits</key>
    <dict>
        <key>NumberOfFiles</key>
        <integer>4096</integer>
    </dict>

    <key>StandardOutPath</key>
    <string>/Users/[User]/Library/Logs/fuel-sidecar.out</string>
    <key>StandardErrorPath</key>
    <string>/Users/[User]/Library/Logs/fuel-sidecar.err</string>
</dict>
</plist>
```
</details>

## Run the Sequencer

At this point you should already be able to run `cosmovisor run start` to run the Sequencer. However, **it is highly recommended to run the Sequencer as a background service**.

Some examples are provided below for Linux and Mac. You will need to replicate the environment variables defined when setting up Cosmovisor.

### Linux

On Linux, you can use `systemd` to run the Sequencer in the background. Knowledge of how to use `systemd` is assumed here.

Here's an example service file with some placeholder (`<...>`) values that must be filled-in:

<details>
  <summary>Click me...</summary>

```sh
[Unit]
Description=Sequencer Node
After=network.target

[Service]
Type=simple
User=<USER>
ExecStart=/home/<USER>/go/bin/cosmovisor run start
Restart=on-failure
RestartSec=3
LimitNOFILE=4096

Environment="DAEMON_NAME=fuelsequencerd"
Environment="DAEMON_HOME=/home/<USER>/.fuelsequencer"
Environment="DAEMON_ALLOW_DOWNLOAD_BINARIES=true"
Environment="DAEMON_LOG_BUFFER_SIZE=512"
Environment="DAEMON_RESTART_AFTER_UPGRADE=true"
Environment="UNSAFE_SKIP_BACKUP=true"
Environment="DAEMON_SHUTDOWN_GRACE=15s"

[Install]
WantedBy=multi-user.target
```
</details>

### Mac

On Mac, you can use `launchd` to run the Sequencer in the background. Knowledge of how to use `launchd` is assumed here.

Here's an example plist file with some placeholder (`[...]`) values that must be filled-in:

<details>
  <summary>Click me...</summary>

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
<dict>
    <key>Label</key>
    <string>fuel.sequencer</string>

    <key>ProgramArguments</key>
    <array>
        <string>/Users/[User]/go/bin/cosmovisor</string>
        <string>run</string>
        <string>start</string>
    </array>

    <key>UserName</key>
    <string>[User]</string>

    <key>EnvironmentVariables</key>
    <dict>
        <key>DAEMON_NAME</key>
        <string>fuelsequencerd</string>
        <key>DAEMON_HOME</key>
        <string>/Users/[User]/.fuelsequencer</string>
        <key>DAEMON_ALLOW_DOWNLOAD_BINARIES</key>
        <string>true</string>
        <key>DAEMON_LOG_BUFFER_SIZE</key>
        <string>512</string>
        <key>DAEMON_RESTART_AFTER_UPGRADE</key>
        <string>true</string>
        <key>UNSAFE_SKIP_BACKUP</key>
        <string>true</string>
        <key>DAEMON_SHUTDOWN_GRACE</key>
        <string>15s</string>
    </dict>

    <key>KeepAlive</key>
    <dict>
        <key>SuccessfulExit</key>
        <false/>
    </dict>

    <key>HardResourceLimits</key>
    <dict>
        <key>NumberOfFiles</key>
        <integer>4096</integer>
    </dict>

    <key>StandardOutPath</key>
    <string>/Users/[User]/Library/Logs/fuel-sequencer.out</string>
    <key>StandardErrorPath</key>
    <string>/Users/[User]/Library/Logs/fuel-sequencer.err</string>
</dict>
</plist>
```
</details>

## Creating an Account

To run a validator, you will need to have a Sequencer account address. Generate an address with a key name:

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

Adding the `0x` prefix to the address in the first line gives you your Ethereum-compatible address, used to deposit into and interact with your Sequencer address from Ethereum. In this case, it's `0xFF8162F37072354EB1E222084DA0D4221E93550F`. To fund your Ethereum-compatible address with Fuel tokens from Ethereum you can use the [DepositFor](https://etherscan.io/address/0xca0c6B264f0F9958Ec186eb2EAa208966187D866#writeProxyContract#F5) function with `recipient` set to your Ethereum-compatible address, in this example `0xFF8162F37072354EB1E222084DA0D4221E93550F`.

> **WARNING**: always test transfer small amounts first if you are going to bridge FUEL tokens to this Ethereum-compatible address.

## Create the Validator

To create the validator, a prerequisite is to have at least 1FUEL, with enough extra to pay for gas fees. You can check your balance from the explorer.

[//]: # (TODO: steps on how to deposit FUEL tokens to a Sequencer address)

Once you have FUEL tokens, run the following to create a validator, using the name of the account that you created in the previous steps:

```sh
fuelsequencerd tx staking create-validator path/to/validator.json \
    --from <NAME> \
    --gas auto \
    --gas-prices 10fuel \
    --gas-adjustment 1.5 \
    --chain-id seq-mainnet-1
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

- The Sequencer should show block syncing.
- The Sidecar should show block extraction. Occasionally it also receives requests for events.

### Tendermint KMS

If you will be using `tmkms`, make sure that in the config:

- Chain ID is set to `seq-mainnet-1` wherever applicable
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

## References

Based on material from:

- https://docs.cosmos.network/main/tooling/cosmovisor
- https://docs.osmosis.zone/networks/join-mainnet/#set-up-cosmovisor

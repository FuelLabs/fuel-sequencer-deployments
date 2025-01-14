# Run Sequencer Node

- [Prerequisites](#prerequisites)
- [Run the Node](#run-the-node)

## Prerequisites

Unless otherwise configured, at least the following ports should be available:

- **Sequencer**: 26656, 26657, 9090, 1317

The minimum requirements for the **Sequencer** are:
 
- 4 cores
- 8 GB ram
- 200GB disk space

The guide assumes that Golang is installed in order to run Cosmovisor. We recommend installing version `1.21+`.

## Run the Node

Obtain binary and genesis from this repository:

- Binary from: https://github.com/FuelLabs/fuel-sequencer-deployments/releases/tag/seq-testnet-2-improved-sidecar
- Genesis from: https://github.com/FuelLabs/fuel-sequencer-deployments/blob/main/seq-testnet-2/genesis.json

Download the right binary based on your architecture to `$GOPATH/bin/` with the name `fuelsequencerd`:

- `echo $GOPATH` to ensure it exists. If not, `go` might not be installed.
- `mkdir $GOPATH/bin/` if the directory does not exist.
- `wget <url/to/binary>` to download the binary.
- `cp <binary> $GOPATH/bin/fuelsequencerd`

Try the binary:

```sh
fuelsequencerd version  # expect seq-testnet-2-improved-sidecar
```

Initialise node directory:

```sh
fuelsequencerd init <node-name> --chain-id seq-testnet-2
```

Copy the downloaded genesis file to `~/.fuelsequencer/config/genesis.json`:

```sh
cp <path/to/genesis.json> ~/.fuelsequencer/config/genesis.json
```

Install and configure Cosmovisor:

- Install Cosmovisor: `go install cosmossdk.io/tools/cosmovisor/cmd/cosmovisor@latest`.
- Set environment variables:
    ```bash
    echo "# Setup Cosmovisor" >> ~/.profile
    echo "export DAEMON_NAME=fuelsequencerd" >> ~/.profile
    echo "export DAEMON_HOME=$HOME/.fuelsequencer" >> ~/.profile
    echo "export DAEMON_ALLOW_DOWNLOAD_BINARIES=true" >> ~/.profile
    echo "export DAEMON_LOG_BUFFER_SIZE=512" >> ~/.profile
    echo "export DAEMON_RESTART_AFTER_UPGRADE=true" >> ~/.profile
    echo "export UNSAFE_SKIP_BACKUP=true" >> ~/.profile
    echo "export DAEMON_SHUTDOWN_GRACE=15s" >> ~/.profile
  
    # Check https://docs.cosmos.network/main/tooling/cosmovisor for more configuration options.
    ```
- Apply environment variables: `source ~/.profile`.
- You can also repeat the above steps but for `~/.bashrc` instead of `~/.profile`.
- Initialise Cosmovisor directories: `cosmovisor init <path/to/fuelsequencerd/binary>` (hint: `whereis fuelsequencerd` for the path).

Configure the node (part 1: `~/.fuelsequencer/config/app.toml`):

- Set `minimum-gas-prices = "10fuel"`.
- Configure `[sidecar]`:
  - Ensure that `enabled = false`.

Configure the node (part 2: `~/.fuelsequencer/config/config.toml`):

- Configure `[p2p]`:
  - Set `persistent_peers = "3a0b4118c01addd33d5add81783805d5add2fb17@80.64.208.17:26656"`.
- Configure `[mempool]`:
  - Set `max_tx_bytes = 1153434` (1.1MiB)
  - Set `max_txs_bytes = 23068670` (~22MiB)
- Configure `[rpc]`:
  - Set `max_body_bytes = 1153434` (optional - relevant for public RPC).

> Note: Ensuring consistent CometBFT mempool parameters across all network nodes is important to reduce transaction delays. This includes `mempool.size`, `mempool.max_txs_bytes`, and `mempool.max_tx_bytes` in [config.toml](https://docs.cometbft.com/v0.38/core/configuration) and `minimum-gas-prices` in [app.toml](https://docs.cosmos.network/main/learn/advanced/config), as pointed out above.

Configure [State Sync](https://fuel-seq.simplystaking.xyz/fuel-testnet/statesync) to get synced up faster. Note that:

- You will need to specify at least two comma-separated RPC servers. You can either refer to the list of alternate RPC servers above or use the same one twice.

At this point `cosmovisor run` will be the equivalent of running `fuelsequencerd`. In fact, to run the node you can use `cosmovisor run start`.

**It is highly recommended to run the Sequencer as a service**, so that it can run in the background. You will need to replicate the environment variables defined above.

Here's an example service file with some placeholder (`<...>`) values:

```bash
[Unit]
Description=FuelSequencer Validator
After=network.target

[Service]
Type=simple
User=<USER>
ExecStart=<HOME>/go/bin/cosmovisor run start --home <NODE-HOME>
Restart=on-failure
RestartSec=3
LimitNOFILE=4096

Environment="DAEMON_NAME=fuelsequencerd"
Environment="DAEMON_HOME=/home/<USER>/.fuelsequencer"  # Double-check this!
Environment="DAEMON_ALLOW_DOWNLOAD_BINARIES=true"
Environment="DAEMON_LOG_BUFFER_SIZE=512"
Environment="DAEMON_RESTART_AFTER_UPGRADE=true"
Environment="UNSAFE_SKIP_BACKUP=true"
Environment="DAEMON_SHUTDOWN_GRACE=15s"

[Install]
WantedBy=multi-user.target
```

## References

Based on material from:

- https://docs.cosmos.network/main/tooling/cosmovisor
- https://docs.osmosis.zone/networks/join-mainnet/#set-up-cosmovisor

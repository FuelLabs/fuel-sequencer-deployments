# Run Sequencer Node

- [Prerequisites](#prerequisites)
- [Run the Node](#run-the-node)
  - [Install Cosmovisor](#install-cosmovisor)
  - [Configure State Sync](#configure-state-sync)
  - [Running the Sequencer](#running-the-sequencer)
    - [Linux](#linux)
    - [Mac](#mac)
- [References](#references)

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

- Binary from: https://github.com/FuelLabs/fuel-sequencer-deployments/releases/tag/seq-testnet-2.3
- Genesis from: https://github.com/FuelLabs/fuel-sequencer-deployments/blob/main/seq-testnet-2/genesis.json

Download the right binary based on your architecture to `$GOPATH/bin/` with the name `fuelsequencerd`:

- `echo $GOPATH` to ensure it exists. If not, `go` might not be installed.
- `mkdir $GOPATH/bin/` if the directory does not exist.
- `wget <url/to/binary>` to download the binary, or any equivalent approach.
- `cp <binary> $GOPATH/bin/fuelsequencerd`

Try the binary:

```sh
fuelsequencerd version  # expect seq-testnet-2.3
```

Initialise node directory:

```sh
fuelsequencerd init <node-name> --chain-id seq-testnet-2
```

Copy the downloaded genesis file to `~/.fuelsequencer/config/genesis.json`:

```sh
cp <path/to/genesis.json> ~/.fuelsequencer/config/genesis.json
```

Configure the node (part 1: `~/.fuelsequencer/config/app.toml`):

- Set `minimum-gas-prices = "10test"`.
- Configure `[sidecar]`:
  - Ensure that `enabled = false`.

Configure the node (part 2: `~/.fuelsequencer/config/config.toml`):

- Configure `[p2p]`:
  - Set `persistent_peers = "25bd839624c4044764446a9241fbfb295d1e2233@80.64.208.18:26656,a2243658040b2c65a5d023a74093cf0bb20094e0@50.21.173.105:26656"`.
- Configure `[mempool]`:
  - Set `max_tx_bytes = 1153434` (1.1MiB)
  - Set `max_txs_bytes = 23068670` (~22MiB)
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

The last three values can be obtained from [the explorer](https://fuel-seq.simplystaking.xyz/fuel-testnet/statesync).

You will need to specify at least two comma-separated RPC servers in `rpc_servers`. You can either refer to the list of alternate RPC servers above or use the same one twice.

### Running the Sequencer

At this point you should already be able to run `cosmovisor run start` to run the Sequencer. However, **it is highly recommended to run the Sequencer as a background service**.

Some examples are provided below for Linux and Mac. You will need to replicate the environment variables defined when setting up Cosmovisor.

#### Linux

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

#### Mac

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

## References

Based on material from:

- https://docs.cosmos.network/main/tooling/cosmovisor
- https://docs.osmosis.zone/networks/join-mainnet/#set-up-cosmovisor

# Scheduled Upgrades

- [Features and Optimisations](#features-and-optimisations)

## Features and Optimisations

- **Version before upgrade**: `seq-testnet-2`
- **Version after upgrade**: `seq-testnet-2.2`
- **Upgrade height**: **`1987700`**
- Estimated upgrade time: `2025-04-10 at 14:00:00 CET`

### Upgrade Instructions (with Cosmovisor)

> Note: the `DAEMON_ALLOW_DOWNLOAD_BINARIES` option is not possible. This means that the binary will NOT be downloaded automatically!

You will need to run through the following steps:

- Download new binary from **TODO** or obtain it from a reputable source.
- Apply environment variables: `source ~/.profile` (refer to [README](../README.md) if you do not have this).
- Register the upgrade: `cosmovisor add-upgrade features-and-optimisations <path-to-downloaded-fuelsequencerd-binary>`.
- From here on, the upgrade process is expected to take place automatically.

### Upgrade Instructions (without Cosmovisor)

The recommended steps to upgrade to the new version without Cosmovisor are as follows:

- Download new binary from **TODO** or obtain it from a reputable source.
- At the upgrade height, once your node has stopped automatically, back up the `.fuelsequencer` directory, especially the `.fuelsequencer/data/priv_validator_state.json` file.
- Swap the old binary with the downloaded binary, and restart your node.
- From here on, the upgrade process is expected to take place automatically.

### Sidecar

It is critical to switch to use the new binary for the Sidecar as well. You will need to follow these steps:

- Download new binary from **TODO** or obtain it from a reputable source.
- Before the upgrade height (i.e. you can do it now):
  - Stop the Sidecar process.
  - Swap the old binary with the downloaded binary.
  - Restart the Sidecar process.

### Recovery

In the event that the upgrade does not succeed, the network maintainers will coordinate the recovery of the network. It is very important to be available and follow the instructions provided by the network maintainers.

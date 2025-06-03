# Scheduled Upgrades

- **Version before upgrade**: `seq-mainnet-1.2` or `seq-mainnet-1.2-improved-sidecar`
- **Version after upgrade**: `seq-mainnet-1.3`
- **Upgrade height**: **`2988700`**
- **Estimated upgrade time**: `2025-06-09 15:00:00 CET`

### Upgrade Instructions (with Cosmovisor)

> Note: the `DAEMON_ALLOW_DOWNLOAD_BINARIES` option is not possible. This means that the binary will NOT be downloaded automatically!

You will need to run through the following steps:

- Download the binary [from the appropriate release](https://github.com/FuelLabs/fuel-sequencer-deployments/releases) or obtain it from a reputable source.
- Apply environment variables: `source ~/.profile` (refer to [README](../README.md) if you do not have this).
- Register the upgrade: `cosmovisor add-upgrade features-and-optimisations <path-to-downloaded-fuelsequencerd-binary>`.
- From here on, the upgrade process is expected to take place automatically.

### Upgrade Instructions (without Cosmovisor)

The recommended steps to upgrade to the new version without Cosmovisor are as follows:

- Download the binary [from the appropriate release](https://github.com/FuelLabs/fuel-sequencer-deployments/releases) or obtain it from a reputable source.
- At the upgrade height, once your node has stopped automatically, back up the `.fuelsequencer` directory, especially the `.fuelsequencer/data/priv_validator_state.json` file.
- Swap the old binary with the downloaded binary, and restart your node.
- From here on, the upgrade process is expected to take place automatically.

### Sidecar

It is not critical to use the new binary for the Sidecar, however this is still recommended, to have a consistent binary version.

### Recovery

In the event that the upgrade does not succeed, the network maintainers will coordinate the recovery of the network. It is very important to be available and follow the instructions provided by the network maintainers.

# Scheduled Upgrades

- [Vesting Accounts Staking](#vesting-accounts-staking)

## Vesting Accounts Staking

- **Version before upgrade**: `seq-mainnet-1.1`
- **Version after upgrade**: `seq-mainnet-1.2` (binaries from **_N/A_**)
- **Upgrade height**: **`370150`**
- Estimated upgrade time: `2024-12-16 at 15:00:00 CET`

### Upgrade Instructions (with Cosmovisor)

> Note: the `DAEMON_ALLOW_DOWNLOAD_BINARIES` option is not possible since the binaries are not public. This means that the binary will NOT be downloaded automatically!

You will need to run through the following steps:

- Download new binary from **_N/A_** or obtain it from a reputable source.
- Apply environment variables: `source ~/.profile` (refer to [README](../README.md) if you do not have this).
- Register the upgrade: `cosmovisor add-upgrade vesting-accounts-staking <path-to-downloaded-fuelsequencerd-binary>`.
- From here on, the upgrade process is expected to take place automatically.

### Upgrade Instructions (without Cosmovisor)

The recommended steps to upgrade to the new version without Cosmovisor are as follows:

- Download new binary from **_N/A_** or obtain it from a reputable source.
- At the upgrade height, once your node has stopped automatically, back up the `.fuelsequencer` directory, especially the `.fuelsequencer/data/priv_validator_state.json` file.
- Swap the old binary with the downloaded binary, and restart your node.
- From here on, the upgrade process is expected to take place automatically.

### Sidecar

It is not critical to use the new binary for the Sidecar, however this is still recommended, to have a consistent binary version.

### Recovery

In the event that the upgrade does not succeed, the network maintainers will coordinate the recovery of the network. It is very important to be available and follow the instructions provided by the network maintainers.

# Scheduled Upgrades

- [Features and Optimisations](#features-and-optimisations)
- [Upgrade Instructions](#upgrade-instructions)
  - [Sidecar](#sidecar)
  - [Sequencer Node](#sequencer-node)
    - [With Cosmovisor](#with-cosmovisor)
    - [Without Cosmovisor](#without-cosmovisor)
  - [Recovery](#recovery)

## Features and Optimisations

- **Version before upgrade**: `seq-mainnet-1.2` or `seq-mainnet-1.2-improved-sidecar`
- **Version after upgrade**: `seq-mainnet-1.3`
- **Upgrade height**: **`2988700`**
- **Estimated upgrade time**: `2025-06-09 15:00:00 CET`

## Upgrade Instructions

The [Sidecar](#sidecar) is backwards compatible with the version before the upgrade, so it is **strongly recommended** to upgrade it beforehand.

Problems might arise if the current version of the sidecar is running with the upgraded sequencer.

The [Sequencer](#sequencer-node) is intended to be upgraded post-upgrade height.

### Sidecar

It is critical to switch to use the new binary for the Sidecar as well. You will need to follow these steps:

- Download new binary from [this release](https://github.com/FuelLabs/fuel-sequencer-deployments/releases/tag/seq-mainnet-1.3) or obtain it from a reputable source.
- You can check that you downloaded the correct binary and that it works by running `fuelsequencerd-<...> version` which should give `seq-mainnet-1.3`.
- Before the upgrade height (i.e. you can do it NOW since the sidecar is backwards compatible):
  - Stop the Sidecar process.
  - Swap the old binary with the downloaded binary.
  - Restart the Sidecar process.

### Sequencer Node

#### With Cosmovisor

> [!CAUTION]
> Note: the `DAEMON_ALLOW_DOWNLOAD_BINARIES` option is not possible. This means that the binary will NOT be downloaded automatically!

You will need to run through the following steps:

- Download new binary from [this release](https://github.com/FuelLabs/fuel-sequencer-deployments/releases/tag/seq-mainnet-1.3) or obtain it from a reputable source.
- You can check that you downloaded the correct binary and that it works by running `fuelsequencerd-<...> version` which should give `seq-mainnet-1.3`. If it doesn't work, you might need to make it executable, e.g. by running `chmod +x seq-mainnet-1.3`.
- Apply Cosmovisor environment variables: `source ~/.profile`. These might already be applied through `~/.bashrc` or `~/.zshrc`. Refer to [install cosmovisor section](./RUN_NODE.md#install-cosmovisor) if in doubt.
- Register the upgrade: `cosmovisor add-upgrade features-and-optimisations <path-to-downloaded-fuelsequencerd-binary>`.
- From here on, the upgrade process is expected to take place automatically.
- Make sure to also follow the *Sidecar Upgrade Instructions* if you're running the Sidecar.

#### Without Cosmovisor

The recommended steps to upgrade to the new version without Cosmovisor are as follows:

- Download new binary from [this release](https://github.com/FuelLabs/fuel-sequencer-deployments/releases/tag/seq-mainnet-1.3) or obtain it from a reputable source.
- You can check that you downloaded the correct binary and that it works by running `fuelsequencerd-<...> version` which should give `seq-mainnet-1.3`. If it doesn't work, you might need to make it executable, e.g. by running `chmod +x seq-mainnet-1.3`.
- At the upgrade height, once your node has stopped automatically, back up the `.fuelsequencer` directory, especially the `.fuelsequencer/data/priv_validator_state.json` file.
- Swap the old binary with the downloaded binary, and restart your node.
- From here on, the upgrade process is expected to take place automatically.
- Make sure to also follow the *Sidecar Upgrade Instructions* if you're running the Sidecar.

### Recovery

In the event that the upgrade does not succeed, the network maintainers will coordinate the recovery of the network. It is very important to be available and follow the instructions provided by the network maintainers.

# Scheduled Upgrades

- [Multi-Vesting Accounts](#multi-vesting-accounts)
- [Upgrade Instructions](#upgrade-instructions)
- [Sequencer Node](#sequencer-node)
    - [With Cosmovisor](#with-cosmovisor)
    - [Without Cosmovisor](#without-cosmovisor)
- [Sidecar](#sidecar)
- [Recovery](#recovery)

## Multi-Vesting Accounts

- **Version before upgrade**: `seq-mainnet-1.3`, `seq-mainnet-1.3.1`, `seq-mainnet-1.3.2`, `seq-mainnet-1.3.3`
- **Version after upgrade**: `seq-mainnet-1.4`
- **Upgrade height**: **`3829100`**
- **Estimated upgrade time**: `2025-08-04 15:00:00 CET`
- [Explorer Countdown](https://fuel-seq.simplystaking.xyz/fuel-mainnet/block/3829100)

## Upgrade Instructions

## Sequencer Node

#### With Cosmovisor

> [!CAUTION]
> Note: the `DAEMON_ALLOW_DOWNLOAD_BINARIES` option is not possible. This means that the binary will NOT be downloaded automatically!

You will need to run through the following steps:

- Download new binary from [this release](https://github.com/FuelLabs/fuel-sequencer-deployments/releases/tag/seq-mainnet-1.4) or obtain it from a reputable source.
- You can check that you downloaded the correct binary and that it works by running `fuelsequencerd-<...> version` which should give `seq-mainnet-1.4`. If it doesn't work, you might need to make it executable, e.g. by running `chmod +x fuelsequencerd-seq-mainnet-1.4-<os>-<arch>`.
- Apply Cosmovisor environment variables: `source ~/.profile`. These might already be applied through `~/.bashrc` or `~/.zshrc`. Refer to [install cosmovisor section](./RUN_NODE.md#install-cosmovisor) if in doubt.
- Register the upgrade: `cosmovisor add-upgrade multi-vesting-accounts <path-to-downloaded-fuelsequencerd-binary>`.
- From here on, the upgrade process is expected to take place automatically.
- Make sure to also follow the *Sidecar Upgrade Instructions* if you're running the Sidecar.

#### Without Cosmovisor

The recommended steps to upgrade to the new version without Cosmovisor are as follows:

- Download new binary from [this release](https://github.com/FuelLabs/fuel-sequencer-deployments/releases/tag/seq-mainnet-1.4) or obtain it from a reputable source.
- You can check that you downloaded the correct binary and that it works by running `fuelsequencerd-<...> version` which should give `seq-mainnet-1.4`. If it doesn't work, you might need to make it executable, e.g. by running `chmod +x fuelsequencerd-seq-mainnet-1.4-<os>-<arch>`.
- At the upgrade height, once your node has stopped automatically, back up the `.fuelsequencer` directory, especially the `.fuelsequencer/data/priv_validator_state.json` file.
- Swap the old binary with the downloaded binary, and restart your node.
- From here on, the upgrade process is expected to take place automatically.
- Make sure to also follow the *Sidecar Upgrade Instructions* if you're running the Sidecar.

## Sidecar

While not critical, it is recommended to switch the Sidecar to use the new binary as well. 

You will need to follow these steps:

- Download new binary from [this release](https://github.com/FuelLabs/fuel-sequencer-deployments/releases/tag/seq-testnet-2.3) or obtain it from a reputable source.
- You can check that you downloaded the correct binary and that it works by running `fuelsequencerd-<...> version` which should give `seq-mainnet-1.4`.
- Before the upgrade height (i.e. you can do it NOW since the sidecar is backwards compatible):
  - Stop the Sidecar process.
  - Swap the old binary with the downloaded binary.
  - Restart the Sidecar process.

## Recovery

In the event that the upgrade does not succeed, the network maintainers will coordinate the recovery of the network. 

It is very important to be available and follow the instructions provided by the network maintainers.

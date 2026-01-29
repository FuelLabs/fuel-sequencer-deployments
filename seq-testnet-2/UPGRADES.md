# <!--Scheduled--> Upgrades

- [Upgrade to seq-testnet-2.6.1](#upgrade-to-seq-testnet-261)
  - [Node Patching Instructions](#node-patching-instructions)
  - [Sidecar Upgrade Instructions](#sidecar-upgrade-instructions)
  - [Recovery](#recovery)

## Upgrade to seq-testnet-2.6.1

> [!NOTE]
> `seq-testnet-2.5` was a scheduled upgrade that as of this writing, has been applied to the network.
>
> `seq-testnet-2.6.1` includes a patch for `seq-testnet-2.5` and `seq-testnet-2.6`, but a scheduled upgrade (proposal) will NOT take place. Instead, this is a binary that you should apply now.
>
> If you haven’t upgraded from `seq-testnet-2.4` yet, you should upgrade to `seq-testnet-2.5` first, by following this guide: https://github.com/FuelLabs/fuel-sequencer-deployments/blob/7827840552713ad1413681213498f145355f547d/seq-testnet-2/UPGRADES.md. Afterwards you can apply the new binary for `seq-testnet-2.6.1`. Binary `seq-testnet-2.6` is NOT valid and should be skipped.

- **Version before upgrade**: `seq-testnet-2.5`, `seq-testnet-2.6`
- **Version after upgrade**: `seq-testnet-2.6.1`
<!-- - **Upgrade name**: `maintenance-patch-202601` (Register this name for the Cosmovisor upgrade)
- **Upgrade height**: **`TBD`**
- **Estimated upgrade time**: [TBD](https://dateful.com/convert/central-european-time-cet?TBD)
- [Explorer Countdown](https://fuel-seq.simplystaking.xyz/fuel-testnet/block/TBD) -->

### Node Patching Instructions

<!-- #### With Cosmovisor

> [!CAUTION]
> Note: the `DAEMON_ALLOW_DOWNLOAD_BINARIES` option is not possible. This means that the binary will NOT be downloaded automatically!

You will need to run through the following steps:

- Download new binary from [this release](https://github.com/FuelLabs/fuel-sequencer-deployments/releases/tag/seq-testnet-2.6) or obtain it from a reputable source.
- You can check that you downloaded the correct binary and that it works by running `fuelsequencerd-<...> version` which should give `seq-testnet-2.6`. If it doesn't work, you might need to make it executable, e.g. by running `chmod +x fuelsequencerd-seq-testnet-2.6-<os>-<arch>`.
- Apply Cosmovisor environment variables: `source ~/.profile`. These might already be applied through `~/.bashrc` or `~/.zshrc`. Refer to [install cosmovisor section](./RUN_NODE.md#install-cosmovisor) if in doubt.
- Register the upgrade: `cosmovisor add-upgrade <upgrade-name> <path-to-downloaded-fuelsequencerd-binary>`. The upgrade name is included in the summary at the top.
- From here on, the upgrade process is expected to take place automatically.
- Make sure to also follow the *Sidecar Upgrade Instructions* if you're running the Sidecar. -->

The recommended steps to patch to the new version, is as follows:

- Download new binary from [this release](https://github.com/FuelLabs/fuel-sequencer-deployments/releases/tag/seq-testnet-2.6.1) or obtain it from a reputable source.
- You can check that you downloaded the correct binary and that it works by running `fuelsequencerd-<...> version` which should give `seq-testnet-2.6.1`.
- At the upgrade height, once your node has stopped automatically, back up the `.fuelsequencer` directory, especially the `.fuelsequencer/data/priv_validator_state.json` file.
- Swap the old binary with the downloaded binary, and restart your node.
- From here on, the upgrade process is expected to take place automatically.
- Make sure to also follow the *Sidecar Upgrade Instructions* if you're running the Sidecar.

### Sidecar Upgrade Instructions

> [!IMPORTANT]
> The Sidecar is an optional change upgrade. 
> Previous version will still work, but it is recommended to upgrade to the new version to take advantage of the latest updates. 
<!-- > Can be done at any time, before or after the upgrade height. -->

You will need to follow these steps:

- Download new binary from [this release](https://github.com/FuelLabs/fuel-sequencer-deployments/releases/tag/seq-testnet-2.6.1) or obtain it from a reputable source.
- You can check that you downloaded the correct binary and that it works by running `fuelsequencerd-<...> version` which should give `seq-testnet-2.6.1`.
<!-- - Before the upgrade height (i.e. you can do it NOW since the sidecar is backwards compatible): -->
- For deploying the new version, you will need to:
  - Stop the Sidecar process.
  - Swap the old binary with the downloaded binary.
  - Restart the Sidecar process.

### Recovery

In the event that the upgrade does not succeed, contact the network maintainers and they will coordinate the recovery of the network.

<!-- It is very important to be available and follow the instructions provided by the network maintainers. -->

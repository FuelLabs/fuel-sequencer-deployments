# Run Sequencer Archive Node

- [Overview](#overview)
- [Configure Archive Mode](#configure-archive-mode)
- [Export Archive Data](#export-archive-data)
- [Import Archive Data](#import-archive-data)
- [Bootstrap a Full Archive Node](#bootstrap-a-full-archive-node)
- [Verify Sync and History](#verify-sync-and-history)

## Overview

This guide covers how to run an archive node for `seq-mainnet-1` and how to migrate archive data between hosts.

An archive node keeps historical state by disabling pruning. This requires significantly more disk space than a regular full node.

## Configure Archive Mode

Use the regular full node setup first: [RUN_NODE.md](./RUN_NODE.md).

Then set these values in `~/.fuelsequencer/config/app.toml`:

- `pruning = "nothing"`
- `pruning-keep-recent = "0"`
- `pruning-keep-every = "0"`
- `pruning-interval = "0"`

If you want complete history from genesis (or from imported archive data), do not use State Sync (`[statesync].enable = false` in `config.toml`).

## Export Archive Data

Run these on the source archive node:

1. Stop the node service.
2. Create a compressed archive of the data directory.
3. Generate a checksum to validate the file after transfer.

```sh
systemctl stop fuelsequencer
tar -czf "/tmp/seq-mainnet-1-archive-data-$(date +%F).tar.gz" -C "$HOME" .fuelsequencer/data
shasum -a 256 "/tmp/seq-mainnet-1-archive-data-$(date +%F).tar.gz" > "/tmp/seq-mainnet-1-archive-data-$(date +%F).sha256"
```

If your service name is different, replace `fuelsequencer` accordingly.

## Import Archive Data

Run these on the destination node:

1. Stop the node service.
2. Verify checksum.
3. Replace the local data directory.
4. Start the node again.

```sh
systemctl stop fuelsequencer
shasum -a 256 -c /tmp/seq-mainnet-1-archive-data-YYYY-MM-DD.sha256
rm -rf "$HOME/.fuelsequencer/data"
tar -xzf /tmp/seq-mainnet-1-archive-data-YYYY-MM-DD.tar.gz -C "$HOME"
systemctl start fuelsequencer
```

The `rm -rf` step is destructive. Make sure you are targeting the correct home directory before running it.

## Bootstrap a Full Archive Node

Recommended flow:

1. Follow [RUN_NODE.md](./RUN_NODE.md) through installation and base config.
2. Enable archive mode as shown above.
3. Import archive data from an existing trusted archive node.
4. Start the node and let it catch up from the imported height to head.

Alternative (slow): start from genesis with archive mode enabled and no State Sync.

## Verify Sync and History

Check sync status:

```sh
curl -s http://127.0.0.1:26657/status
```

Check that old heights are queryable (replace with a known historical height):

```sh
curl -s "http://127.0.0.1:26657/block?height=<historical-height>"
```

If historical blocks return successfully and `catching_up` eventually becomes `false`, the archive node bootstrap is complete.

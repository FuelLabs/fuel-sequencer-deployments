## Monitoring

Both the Sequencer and the Sidecar come with a set of metrics that can be exposed via Prometheus out-of-the-box.

- [Sequencer](#sequencer)
- [Sidecar](#sidecar)

### Sequencer

To enable prometheus metrics on the Sequencer set the following in your configurations:

- config.toml (default: `~/.fuelsequencer/config/config.toml`) -> `instrumentation` -> `prometheus` -> `true`
- config.toml (default: `~/.fuelsequencer/config/config.toml`) -> `instrumentation` -> `prometheus_listen_addr` -> `":26660"`
- app.toml (default: `~/.fuelsequencer/config/app.toml`) -> `telemetry` -> `enabled` -> `true`
- app.toml (default: `~/.fuelsequencer/config/app.toml`) -> `telemetry` -> `prometheus-retention-time` -> `"3600"`

> The `prometheus-retention-time` determines how long the node retains stale values before they get removed from the metrics list until the value gets updated again.

The enabled metrics will show up at `http://<HOST>:26660`.

These are the list of metrics enabled on the Sequencer:

- `sequencer_begin_block_minted_tokens`
- `sequencer_begin_block_supply_current`
- `sequencer_begin_block_supply_last`
- `sequencer_begin_block_supply_offset`
- `sequencer_store_ethereum_event_index_offset`
- `sequencer_store_last_ethereum_nonce`
- `sequencer_store_index_num_injected_txs_total`
- `sequencer_store_last_consensus_txs_sequence`
- `sequencer_ante_handler_injected_msg`
- `sequencer_tx_msg_deposit_from_ethereum`
- `sequencer_tx_msg_withdraw_to_ethereum`
- `sequencer_store_time_since_last_eth_update`

> Note: The metrics above were not described in detail because Sequencer metrics are primarily intended for the Sequencer maintainers.

### Sidecar

To enable Prometheus metrics on the Sidecar, run the Sidecar startup command with the following flags as parameters:

- `--prometheus_enabled true`
- (Optional) `--prometheus_listen_address ":8081"`

The enabled metrics will show up at `http://<HOST>:8081`

These are the list of metrics enabled on the Sidecar:

- `sidecar_core_max_syncable_block`: the last Ethereum block that the Sidecar can sync at the moment.
- `sidecar_core_last_header_seen`: the most recent Ethereum header that the Sidecar detected.
  - If this stops increasing then the Sidecar does not know about new Ethereum headers.
- `sidecar_core_header_delay_seconds_bucket`: delays in receiving Ethereum headers.
  - If this is too high, your Sidecar is taking too long to sync to Ethereum.
- `sidecar_core_start_time`: the time that the Sidecar started as a Unix timestamp in seconds.
  - If this resets this means the Sidecar restarted.
- `sidecar_core_catching_up`: whether the sidecar is catching up to Ethereum. 1 if yes, 0 if no.
- `sidecar_eth_logs_query_delay_seconds_bucket`: how long it takes to receive queried Ethereum logs.
- `sidecar_eth_logs_query_error_count`: how many errors were observed when querying Ethereum logs
- `sidecar_seq_lebs_query_delay_seconds_bucket`: how long it takes to receive queried LastEthereumBlockSynced.
- `sidecar_seq_lebs_query_error_count`: how many errors were observed when querying the LastEthereumBlockSynced.
- `sidecar_store_events_processed`: the number of events processed by the Sidecar.
- `sidecar_store_blocks_pruned`: the number of old Ethereum blocks pruned by the Sidecar.
- `sidecar_store_start_query_block`: the oldest Ethereum block that the Sidecar has in state, if any.
- `sidecar_store_last_synced_block`: the last Ethereum block synced by the Sidecar.
  - If this stops increasing even as new Ethereum blocks are finalised, something is wrong.
  - Note that LastSyncedBlock increases in a staggered manner since the Sidecar needs to wait for a block to be finalised before syncing it.
- `sidecar_service_block_events_requests_total`: how many requests for block events the Sidecar received, with success status.
  - If this is not increasing at every Sequencer block, then the validator is not successfully querying the Sidecar.
  - Note that this metric also reports erroneous requests, which are typically not problematic. The problem would be if it is not increasing at all.

> Note: Node operators should become familiar with the Sidecar metrics as it is the responsibility of the operators to ensure that the Sidecar is functioning correctly.

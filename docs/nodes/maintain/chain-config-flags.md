---
sidebar_position: 6
description: Reference for all available chain config options and flags.
---
# Chain Configs

Some chains allow the node operator to provide a custom configuration. AvalancheGo can read chain configurations from files and pass them to the corresponding chains on initialization.

AvalancheGo looks for these files in the directory specified by `--chain-config-dir` AvalancheGo flag, as documented [here](avalanchego-config-flags.md#--chain-config-dir-string). If omitted, value defaults to `$HOME/.avalanchego/configs/chains`. This directory can have sub-directories whose names are chain IDs or chain aliases. Each sub-directory contains the configuration for the chain specified in the directory name. Each sub-directory should contain a file named `config`, whose value is passed in when the corresponding chain is initialized (see below for extension). For example, config for the C-Chain should be at: `{chain-config-dir}/C/config.json`.

This also applies to subnets, for example, if a subnet's chain id is `2ebCneblahblahblah`, the config for this chain should be at `{chain-config-dir}/2ebCneblahblahblah/config.json`

The filename extension that these files should have, and the contents of these files, is VM-dependent. For example, some chains may expect `config.txt` while others expect `config.json`. If multiple files are provided with the same name but different extensions (e.g. `config.json` and `config.txt`) in the same sub-directory, AvalancheGo will exit with an error.

For a given chain, AvalancheGo will look first for a config sub-directory whose name is the chain ID. If it isn't found, it looks for a config sub-directory whose name is the chain's primary alias. If it's not found, it looks for a config sub-directory whose name is another alias for the chain. All folder and file names are case sensitive.

Alternatively, for some setups it might be more convenient to provide config entirely via the command line. For that, you can use AvalancheGo `--chain-config-dir` flag, as documented [here](avalanchego-config-flags.md#--chain-config-content-string).

It is not required to provide these custom configurations. If they are not provided, a VM-specific default config will be used.

## C-Chain Configs

In order to specify a config for the C-Chain, a JSON config file should be placed at `{chain-config-dir}/C/config.json`.

For example if `chain-config-dir` has the default value which is `$HOME/.avalanchego/configs/chains`, then `config.json` should be placed at `$HOME/.avalanchego/configs/chains/C/config.json`.

The default C-Chain config is:

```json
{
  "snowman-api-enabled": false,
  "coreth-admin-api-enabled": false,
  "coreth-admin-api-dir": "",
  "eth-apis": [
    "public-eth",
    "public-eth-filter",
    "net",
    "web3",
    "internal-public-eth",
    "internal-public-blockchain",
    "internal-public-transaction-pool"
  ],
  "continuous-profiler-dir": "",
  "continuous-profiler-frequency": 900000000000,
  "continuous-profiler-max-files": 5,
  "rpc-gas-cap": 50000000,
  "rpc-tx-fee-cap": 100,
  "preimages-enabled": false,
  "snapshot-async": true,
  "snapshot-verification-enabled": false,
  "pruning-enabled": true,
  "allow-missing-tries": false,
  "populate-missing-tries-parallelism": 1024,
  "metrics-enabled": true,
  "metrics-expensive-enabled": false,
  "local-txs-enabled": false,
  "api-max-duration": 0,
  "ws-cpu-refill-rate": 0,
  "ws-cpu-max-stored": 0,
  "api-max-blocks-per-request": 0,
  "allow-unfinalized-queries": false,
  "allow-unprotected-txs": false,
  "keystore-directory": "",
  "keystore-external-signer": "",
  "keystore-insecure-unlock-allowed": false,
  "remote-tx-gossip-only-enabled": false,
  "tx-regossip-frequency": 60000000000,
  "tx-regossip-max-size": 15,
  "log-level": "info",
  "offline-pruning-enabled": false,
  "offline-pruning-bloom-filter-size": 512,
  "offline-pruning-data-directory": "",
  "max-outbound-active-requests": 8
}
```
Default values are overridden only if specified in the given config file. It is recommended to only provide values which are different from the default, as that makes the config more resilient to future default changes. Otherwise, if defaults change your node will remain with the old values, which might adversely affect your node operation.

### Continuous Profiling

#### `continuous-profiler-dir` (string):

Enables the continuous profiler (captures a CPU/Memory/Lock profile at a specified interval). Defaults to "". If a non-empty string is provided, it enables the continuous profiler and specifies the directory to place the profiles in.

#### `continuous-profiler-frequency` (duration):

Specifies the frequency to run the continuous profiler. Defaults to 15 minutes.

#### `continuous-profiler-max-files` (int):

Specifies the maximum number of profiles to keep before removing the oldest.

### Enabling Avalanche Specific APIs

#### `snowman-api-enabled` (boolean):

Enables the Snowman API. Defaults to false.

#### `coreth-admin-api-enabled` (boolean):

Enables the Admin API. Defaults to false.

#### `coreth-admin-api-dir` (string):

Specifies the directory for the Admin API to use to store CPU/Mem/Lock Profiles. Defaults to "".

### Enabling EVM APIs

#### `eth-apis` ([]string):

Use the `eth-apis` field to specify the exact set of below services to enable on your node. If this field is not set, then the default list will be: `["public-eth","public-eth-filter","net","web3","internal-public-eth","internal-public-blockchain","internal-public-transaction-pool"]`.

Note: if you populate this field, it will override the defaults so you must include every service you wish to enable.

#### `public-eth`:

Adds the following RPC calls to the `eth_*` namespace. Defaults to true.

`eth_coinbase`
`eth_etherbase`

#### `public-eth-filter`:

Enables the public filter API for the `eth_*` namespace. Defaults to true.

Adds the following RPC calls (see https://eth.wiki/json-rpc/API for complete documentation):

- `eth_newPendingTransactionFilter`
- `eth_newPendingTransactions`
- `eth_newAcceptedTransactions`
- `eth_newBlockFilter`
- `eth_newHeads`
- `eth_logs`
- `eth_newFilter`
- `eth_getLogs`
- `eth_uninstallFilter`
- `eth_getFilterLogs`
- `eth_getFilterChanges`

#### `private-admin`:

Adds the following RPC calls to the `admin_*` namespace. Defaults to false.

- `admin_importChain`
- `admin_exportChain`

#### `public-debug`:

Adds the following RPC calls to the `debug_*` namespace. Defaults to false.

- `debug_dumpBlock`
- `debug_accountRange`

#### `private-debug`:

Adds the following RPC calls to the `debug_*` namespace. Defaults to false.

- `debug_preimage`
- `debug_getBadBlocks`
- `debug_storageRangeAt`
- `debug_getModifiedAccountsByNumber`
- `debug_getModifiedAccountsByHash`
- `debug_getAccessibleState`

#### `net`:

Adds the following RPC calls to the `net_*` namespace. Defaults to true.

- `net_listening`
- `net_peerCount`
- `net_version`

Note: Coreth is a virtual machine and does not have direct access to the networking layer, so `net_listening` always returns true and `net_peerCount` always returns 0. For accurate metrics on the network layer, users should use the AvalancheGo APIs.

#### `debug-tracer`:

Adds the following RPC calls to the `debug_*` namespace. Defaults to false.

- `debug_traceChain`
- `debug_traceBlockByNumber`
- `debug_traceBlockByHash`
- `debug_traceBlock`
- `debug_traceBadBlock`
- `debug_intermediateRoots`
- `debug_traceTransaction`
- `debug_traceCall`

#### `web3`:

Adds the following RPC calls to the `web3_*` namespace. Defaults to true.

- `web3_clientVersion`
- `web3_sha3`

#### `internal-public-eth`:

Adds the following RPC calls to the `eth_*` namespace. Defaults to true.

- `eth_gasPrice`
- `eth_baseFee`
- `eth_maxPriorityFeePerGas`
- `eth_feeHistory`

#### `internal-public-blockchain`:

Adds the following RPC calls to the `eth_*` namespace. Defaults to true.

- `eth_chainId`
- `eth_blockNumber`
- `eth_getBalance`
- `eth_getAssetBalance`
- `eth_getProof`
- `eth_getHeaderByNumber`
- `eth_getHeaderByHash`
- `eth_getBlockByNumber`
- `eth_getBlockByHash`
- `eth_getUncleBlockByNumberAndIndex`
- `eth_getUncleBlockByBlockHashAndIndex`
- `eth_getUncleCountByBlockNumber`
- `eth_getUncleCountByBlockHash`
- `eth_getCode`
- `eth_getStorageAt`
- `eth_call`
- `eth_estimateGas`
- `eth_createAccessList`

#### `internal-public-transaction-pool`:

Adds the following RPC calls to the `eth_*` namespace. Defaults to true.

- `eth_getBlockTransactionCountByNumber`
- `eth_getBlockTransactionCountByHash`
- `eth_getTransactionByBlockNumberAndIndex`
- `eth_getTransactionByBlockHashAndIndex`
- `eth_getRawTransactionByBlockNumberAndIndex`
- `eth_getRawTransactionByBlockHashAndIndex`
- `eth_getTransactionCount`
- `eth_getTransactionByHash`
- `eth_getRawTransactionByHash`
- `eth_getTransactionReceipt`
- `eth_sendTransaction`
- `eth_fillTransaction`
- `eth_sendRawTransaction`
- `eth_sign`
- `eth_signTransaction`
- `eth_pendingTransactions`
- `eth_resend`

#### `internal-public-tx-pool`:

Adds the following RPC calls to the `txpool_*` namespace. Defaults to false.

- `txpool_content`
- `txpool_contentFrom`
- `txpool_status`
- `txpool_inspect`

#### `internal-public-debug`:

Adds the following RPC calls to the `debug_*` namespace. Defaults to false.

- `debug_getHeaderRlp`
- `debug_getBlockRlp`
- `debug_printBlock`

#### `internal-private-debug`:

Adds the following RPC calls to the `debug_*` namespace. Defaults to false.

- `debug_chaindbProperty`
- `debug_chaindbCompact`

#### `internal-public-account`:

Adds the following RPC calls to the `eth_*` namespace. Defaults to true.

- `eth_accounts`

#### `internal-private-personal`:

Adds the following RPC calls to the `personal_*` namespace. Defaults to false.

- `personal_listAccounts`
- `personal_listWallets`
- `personal_openWallet`
- `personal_deriveAccount`
- `personal_newAccount`
- `personal_importRawKey`
- `personal_unlockAccount`
- `personal_lockAccount`
- `personal_sendTransaction`
- `personal_signTransaction`
- `personal_sign`
- `personal_ecRecover`
- `personal_signAndSendTransaction`
- `personal_initializeWallet`
- `personal_unpair`

### API Configuration

#### `rpc-gas-cap` (int):

The maximum gas to be consumed by an RPC Call (used in `eth_estimateGas` and `eth_call`). Defaults to 50,000,000.

#### `rpc-tx-fee-cap` (int):

Global transaction fee (price \* gaslimit) cap (measured in AVAX) for send-transaction variants. Defaults to 100.

#### `api-max-duration` (duration):

Maximum API call duration. If API calls exceed this duration, they will time out. Defaults to 0 (no maximum).

#### `api-max-blocks-per-request` (int):

Maximum number of blocks to serve per `getLogs` request. Defaults to 0 (no maximum).

#### `ws-cpu-refill-rate` (duration):

The refill rate specifies the maximum amount of CPU time to allot a single connection per second. Defaults to no maximum (0).

#### `ws-cpu-max-stored` (duration):

Specifies the maximum amount of CPU time that can be stored for a single WS connection. Defaults to no maximum (0).

#### `allow-unfinalized-queries` (boolean):

Allows queries for unfinalized (not yet accepted) blocks/transactions. Defaults to false.

### Transaction Pool

#### `local-txs-enabled` (boolean):

Enables local transaction handling (prioritizes transactions submitted through this node). Defaults to false.

#### `allow-unprotected-txs` (boolean):

If true, the APIs will allow transactions that are not replay protected (EIP-155) to be issued through this node. Defaults to false.

#### `remote-tx-gossip-only-enabled` (boolean):

If true, the node will only gossip remote transactions to prevent transactions issued through this node from being broadcast to the network. Defaults to false.

#### `tx-regossip-frequency` (duration):

Amount of time that should elapse before we attempt to re-gossip a transaction that was already gossiped once. Defaults to 1 minute.

#### `tx-regossip-max-size` (int):

Maximum number of transactions to re-gossip at once. Defaults to 15.

### Metrics

#### `metrics-enabled` (boolean):

Enables metrics. Defaults to false.

#### `metrics-expensive-enabled` (boolean):

Enables expensive metrics. Defaults to false.

### Database

#### `pruning-enabled` (boolean):

If true, database pruning of obsolete historical data will be enabled. Should be disabled for nodes that need access to all data at historical roots. Pruning will be done only for new data. Defaults to `false` in v1.4.9, and `true` in subsequent versions.

#### `preimages-enabled` (boolean):

If true, enables preimages. Defaults to false.

#### `offline-pruning-enabled` (boolean):

If true, offline pruning will run on startup and block until it completes (approximately one hour on mainnet). This will reduce the size of the database by deleting old trie nodes. **While performing offline pruning, your node will not be able to process blocks and will be considered offline.**
While ongoing, the pruning process consumes a small amount of additional disk space (for deletion markers and the bloom filter). For more information see [here.](../maintain/run-offline-pruning.md#disk-space-considerations)

Since offline pruning deletes old state data, this should not be run on nodes that need to support archival API requests.

This is meant to be run manually, so after running with this flag once, it must be toggled back to false before running the node again. Therefore, you should run with this flag set to true and then set it to false on the subsequent run.

#### `offline-pruning-bloom-filter-size` (int):

This flag sets the size of the bloom filter to use in offline pruning (denominated in MB and defaulting to 512 MB). The bloom filter is kept in memory for efficient checks during pruning and is also written to disk to allow pruning to resume withou re-generating the bloom filter.

The active state is added to the bloom filter before iterating the DB to find trie nodes that can be safely deleted, any trie nodes not in the bloom filter are considered safe for deletion. The size of the bloom filter may impact its false positive rate, which can impact the results of offline pruning. This is an advanced parameter that has been tuned to 512 MB and should not be changed without thoughtful consideration.

#### `offline-pruning-data-directory` (string):

This flag must be set when offline pruning is enabled and sets the directory that offline pruning will use to write its bloom filter to disk. This directory should not be changed in between runs until offline pruning has completed.

### Snapshots

#### `snapshot-async` (boolean):

If true, allows snapshot generation to be executed asynchronously. Defaults to true.

#### `snapshot-verification-enabled` (boolean):

If true, verifies the complete snapshot after it has been generated. Defaults to false.

### Log Level

#### `log-level` (string):

Defines the C-chain log level. Must be one of `"trace"`, `"debug"`, `"info"`, `"warn"`, `"error"`, `"crit"`. Defaults to `"info"`.

### Keystore Settings

#### `keystore-directory` (string):

The directory that contains private keys. Can be given as a relative path. If empty, uses a temporary directory at `coreth-keystore`. Defaults to empty string.

#### `keystore-external-signer` (string):

Specifies an external URI for a clef-type signer. Defaults to the empty string (not enabled).

#### `keystore-insecure-unlock-allowed` (bool):

If true, allow users to unlock accounts in unsafe HTTP environment. Defaults to false.

## X-Chain Configs

In order to specify a config for the X-Chain, a JSON config file should be placed at `{chain-config-dir}/X/config.json`.

For example if `chain-config-dir` has the default value which is `$HOME/.avalanchego/configs/chains`, then `config.json` can be placed at `$HOME/.avalanchego/configs/chains/X/config.json`.

This allows you to specify a config to be passed into the X-Chain. The default values for this config are:

```json
{
  "index-transactions": false,
  "index-allow-incomplete": false
}
```

Default values are overridden only if explicitly specified in the config.

The parameters are as follows:

**Transaction Indexing**

### `index-transactions` (boolean):

Enables AVM transaction indexing if set to `true`. Default value is `false`. When set to `true`, AVM transactions are indexed against the `address` and `assetID` involved. This data is available via `avm.getAddressTxs` [API](../../apis/avalanchego/apis/x-chain.mdx#avmgetaddresstxs).

Please note that if `index-transactions` is set to true, it must always be set to true for the node's lifetime. If set to `false` after having been set to `true`, the node will refuse to start unless `index-allow-incomplete` is also set to `true` (see below).

### `index-allow-incomplete` (boolean):

Allows incomplete indices. Default value is `false`.

This config value is ignored if there is no X-Chain indexed data in the DB and `index-transactions` is set to `false`.


## Subnet Configs

As mentioned above, if a subnet's chain id is `2ebCneblahblahblah`, the config for this chain should be at `{chain-config-dir}/2ebCneblahblahblah/config.json`

# Mainnet
* [Snapshot](https://github.com/Vgk88/cyberG-identity/blob/main/Teritori.md#snapshot-mainnet)
* [State Sync](https://github.com/Vgk88/cyberG-identity/blob/main/Teritori.md#state-sync-mainnet)

## Snapshot Mainnet

### Stop the service and reset the data

```
sudo systemctl stop teritorid
cp $HOME/.teritorid/data/priv_validator_state.json $HOME/.teritorid/priv_validator_state.json.backup
rm -rf $HOME/.teritorid/data
```
### RPC
```
http://65.108.41.172:19657
```


### Download latest snapshot
```
cd $HOME/.teritorid/
wget http://65.108.41.172:8000/Snapshot_teritori-main.tar.gz -O - | tar -xz
mv $HOME/.teritorid/priv_validator_state.json.backup $HOME/.teritorid/data/priv_validator_state.json
rm $HOME/.teritorid/Snapshot_teritori-main.tar.gz
```

### Restart the service and check the log
```
sudo systemctl start teritorid && sudo journalctl -u teritorid -f --no-hostname -o cat
```

  
## State Sync Mainnet

<details>
  <summary><b>Pruning</b></summary>

```
# Prune Type
pruning = "custom"

# Prune Strategy
pruning-keep-every = 0

# State-Sync Snapshot Strategy
snapshot-interval = 50
snapshot-keep-recent = 100
```
</details>

```
SNAP_RPC="http://65.108.41.172:19657"
Name_bin="teritorid"
Name_config_file=".teritorid"
Name_service="teritorid"
peers="ae2261521f2f15eb3f98cd8f142768ba7c00f5cc@65.108.41.172:19656"
```

```
LATEST_HEIGHT=$(curl -s $SNAP_RPC/block | jq -r .result.block.header.height); \
BLOCK_HEIGHT=$((LATEST_HEIGHT - 2000)); \
TRUST_HASH=$(curl -s "$SNAP_RPC/block?height=$BLOCK_HEIGHT" | jq -r .result.block_id.hash) && \
echo $LATEST_HEIGHT $BLOCK_HEIGHT $TRUST_HASH
sed -i.bak -E "s|^(enable[[:space:]]+=[[:space:]]+).*$|\1true| ; \
s|^(rpc_servers[[:space:]]+=[[:space:]]+).*$|\1\"$SNAP_RPC,$SNAP_RPC\"| ; \
s|^(trust_height[[:space:]]+=[[:space:]]+).*$|\1$BLOCK_HEIGHT| ; \
s|^(trust_hash[[:space:]]+=[[:space:]]+).*$|\1\"$TRUST_HASH\"| ; \
s|^(seeds[[:space:]]+=[[:space:]]+).*$|\1\"\"|" $HOME/$Name_config_file/config/config.toml

```

```
sudo systemctl stop $Name_service && $Name_bin tendermint unsafe-reset-all --home $HOME/$Name_config_file --keep-addr-book

```

```
sed -i.bak -e  "s/^persistent_peers *=.*/persistent_peers = \"$peers\"/" $HOME/$Name_config_file/config/config.toml

```

```
sudo systemctl restart $Name_service
journalctl -fu $Name_service -o cat

```
## State Sync Testnet

### RPC
```
http://135.181.138.160:46657
```

<details>
  <summary><b>Pruning</b></summary>

```
# Prune Type
pruning = "custom"

# Prune Strategy
pruning-keep-every = 0

# State-Sync Snapshot Strategy
snapshot-interval = 10
snapshot-keep-recent = 100
```
</details>

```
SNAP_RPC="http://135.181.138.160:46657"
Name_bin="teritorid"
Name_config_file=".teritorid"
Name_service="teritorid"
peers="31413c99357d0cfc48a46767ade171db2ea0205e@135.181.138.160:46656"
```

```
LATEST_HEIGHT=$(curl -s $SNAP_RPC/block | jq -r .result.block.header.height); \
BLOCK_HEIGHT=$((LATEST_HEIGHT - 2000)); \
TRUST_HASH=$(curl -s "$SNAP_RPC/block?height=$BLOCK_HEIGHT" | jq -r .result.block_id.hash) && \
echo $LATEST_HEIGHT $BLOCK_HEIGHT $TRUST_HASH
sed -i.bak -E "s|^(enable[[:space:]]+=[[:space:]]+).*$|\1true| ; \
s|^(rpc_servers[[:space:]]+=[[:space:]]+).*$|\1\"$SNAP_RPC,$SNAP_RPC\"| ; \
s|^(trust_height[[:space:]]+=[[:space:]]+).*$|\1$BLOCK_HEIGHT| ; \
s|^(trust_hash[[:space:]]+=[[:space:]]+).*$|\1\"$TRUST_HASH\"| ; \
s|^(seeds[[:space:]]+=[[:space:]]+).*$|\1\"\"|" $HOME/$Name_config_file/config/config.toml

```

```
sudo systemctl stop $Name_service && $Name_bin tendermint unsafe-reset-all --home $HOME/$Name_config_file --keep-addr-book

```

```
sed -i.bak -e  "s/^persistent_peers *=.*/persistent_peers = \"$peers\"/" $HOME/$Name_config_file/config/config.toml

```

```
sudo systemctl restart $Name_service
journalctl -fu $Name_service -o cat

```

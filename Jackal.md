

## Snapshot Mainnet

### Stop the service and reset the data

```
sudo systemctl stop canined
cp $HOME/.canine/data/priv_validator_state.json $HOME/.canine/priv_validator_state.json.backup
rm -rf $HOME/.canine/data
```

### Download latest snapshot
```
cd $HOME/.canine/
wget http://65.108.41.172:8000/Snapshot_Jackal-test.tar.gz -O - | tar -xz
mv $HOME/.canine/priv_validator_state.json.backup $HOME/.canine/data/priv_validator_state.json
rm $HOME/.canine/Snapshot_Jackal-test.tar.gz
```

### Restart the service and check the log
```
sudo systemctl start canined && sudo journalctl -u canined -f --no-hostname -o cat
```


## State Sync Mainnet

```
SNAP_RPC="http://65.108.41.172:29957"
Name_bin="canined"
Name_config_file=".canine"
Name_service="canined"
peers="80cc4b90a546a138a480642dd5ce0fcf65ba2d8c@65.108.41.172:29956"
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
======================================================================================================

## Snapshot Testnet

### Stop the service and reset the data

```
sudo systemctl stop canined
cp $HOME/.canine/data/priv_validator_state.json $HOME/.canine/priv_validator_state.json.backup
rm -rf $HOME/.canine/data
```

### Download latest snapshot
```
cd $HOME/.canine/
wget http://jackal.cyberg.digital/Snapshot_Jackal-test.tar.gz -O - | tar -xz
mv $HOME/.canine/priv_validator_state.json.backup $HOME/.canine/data/priv_validator_state.json
```

### Restart the service and check the log
```
sudo systemctl start canined && sudo journalctl -u canined -f --no-hostname -o cat
```

## State Sync Testnet
```
SNAP_RPC="http://jackal.cyberg.digital:27687"
Name_bin="canined"
Name_config_file=".canine"
Name_service="canined"
peers="ff5171d91cb033670238998dc84bdf69468bb053@51.89.232.234:27686"
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


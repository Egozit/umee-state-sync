# Umee State Sync

#### Public RPC endpoint: http://95.216.76.51:26657/  
#### Public API: http://95.216.76.51:1317/

# Guide to sync your node using State Sync:

## 1. Stop your node  

```
sudo systemctl stop umeed.service
```

## 2. Reset the blockchain database, remove address book, and reset data/priv_validator_state.json to the genesis state, then download new addrbook.json
```
umeed unsafe-reset-all
wget -qO $HOME/.umee/config/addrbook.json https://raw.githubusercontent.com/Egozit/umee-state-sync/main/addrbook.json
```  

## 3. Add variables to store RPC address and Block info requested from our RPC
```
SNAP_RPC1="http://95.216.76.51:26657" \
SNAP_RPC2="http://95.216.76.51:26657" \
LATEST_HEIGHT=$(curl -s $SNAP_RPC2/block | jq -r .result.block.header.height); \
BLOCK_HEIGHT=$((LATEST_HEIGHT - 500)); \
TRUST_HASH=$(curl -s "$SNAP_RPC2/block?height=$BLOCK_HEIGHT" | jq -r .result.block_id.hash)
```

## 4. Check the output of the variables (variables must be non-empy)
```
echo $LATEST_HEIGHT $BLOCK_HEIGHT $TRUST_HASH
```

## 5. Enable State Sync mode
```
sed -i.bak -E "s|^(enable[[:space:]]+=[[:space:]]+).*$|\1true| ; \
s|^(rpc_servers[[:space:]]+=[[:space:]]+).*$|\1\"$SNAP_RPC1,$SNAP_RPC2\"| ; \
s|^(trust_height[[:space:]]+=[[:space:]]+).*$|\1$BLOCK_HEIGHT| ; \
s|^(trust_hash[[:space:]]+=[[:space:]]+).*$|\1\"$TRUST_HASH\"|" $HOME/.umee/config/config.toml
```

## 6. Add our RPC as a persistent peer
```
peers="ed0b7f7d06444daa07514edd10de51405a6cfba9@95.216.76.51:26657" \
&& sed -i.bak -e "s/^persistent_peers *=.*/persistent_peers = \"$peers\"/" $HOME/.umee/config/config.toml 
```

## 7. Restart your node and follow the logs
```
sudo systemctl restart umeed.service && sudo journalctl -u umeed.service -f -o cat
```

## 8. After node's full synchronization disable the State Sync mod
```
sed -i.bak -E "s|^(enable[[:space:]]+=[[:space:]]+).*$|\1false|" $HOME/.umee/config/config.toml
```

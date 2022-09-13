# Umee State Sync

Public RPC endpoint: http://95.216.2.219:26657/  
Public API: http://95.216.2.219:1317/

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

## 3. ADD variables to store RPC address and Block info requested from our RPC
```
SNAP_RPC1="http://213.246.45.190:26657" \
SNAP_RPC2="http://213.246.45.190:26657" \
LATEST_HEIGHT=$(curl -s $SNAP_RPC2/block | jq -r .result.block.header.height); \
BLOCK_HEIGHT=$((LATEST_HEIGHT - 500)); \
TRUST_HASH=$(curl -s "$SNAP_RPC2/block?height=$BLOCK_HEIGHT" | jq -r .result.block_id.hash)
```

## 4. Check the output of the variables (variables must be non-empy)
```
echo $LATEST_HEIGHT $BLOCK_HEIGHT $TRUST_HASH
```

## 5. Enable State Sync mode

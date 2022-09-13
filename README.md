# Umee State Sync

Public RPC endpoint: http://95.216.2.219:26657/  
Public API: http://95.216.2.219:1317/

# Guide to sync your node using State Sync:

## 1. Stop your node  

```
sudo systemctl stop umeed.service
```

## 2. Reset the blockchain database, remove address book, and reset data/priv_validator_state.json to the genesis state, then download new addrbook.json


# Lava-Snapshots (Daily update)

    sudo systemctl stop lavad

    cp $HOME/.lava/data/priv_validator_state.json $HOME/.lava/priv_validator_state.json.backup 

    lavad tendermint unsafe-reset-all --home $HOME/.lava --keep-addr-book 
    curl https://snapshot.lava.aknodes.net/snapshot-lava.AKNodes.lz4 | lz4 -dc - | tar -xf - -C $HOME/.lava

    mv $HOME/.lava/priv_validator_state.json.backup $HOME/.lava/data/priv_validator_state.json 

    sudo systemctl start lavad
    sudo journalctl -u lavad -f --no-hostname -o cat


# State Sync

    sudo systemctl stop lavad
    cp $HOME/.lava/data/priv_validator_state.json $HOME/.lava/priv_validator_state.json.backup
    lavad tendermint unsafe-reset-all --home $HOME/.lava
    
    
    STATE_SYNC_RPC=https://rpc.lava.aknodes.net:443
    STATE_SYNC_PEER=a7cad1d8aa2b5fa2070c826307cdaf09bbf114f6@212.227.233.231:36656
    LATEST_HEIGHT=$(curl -s $STATE_SYNC_RPC/block | jq -r .result.block.header.height)
    SYNC_BLOCK_HEIGHT=$(($LATEST_HEIGHT - 1000))
    SYNC_BLOCK_HASH=$(curl -s "$STATE_SYNC_RPC/block?height=$SYNC_BLOCK_HEIGHT" | jq -r .result.block_id.hash)

    sed -i \
      -e "s|^enable *=.*|enable = true|" \
      -e "s|^rpc_servers *=.*|rpc_servers = \"$STATE_SYNC_RPC,$STATE_SYNC_RPC\"|" \
      -e "s|^trust_height *=.*|trust_height = $SYNC_BLOCK_HEIGHT|" \
      -e "s|^trust_hash *=.*|trust_hash = \"$SYNC_BLOCK_HASH\"|" \
      -e "s|^persistent_peers *=.*|persistent_peers = \"$STATE_SYNC_PEER\"|" \
      $HOME/.lava/config/config.toml

    mv $HOME/.lava/priv_validator_state.json.backup $HOME/.lava/data/priv_validator_state.json
   
    sudo systemctl start lavad && sudo journalctl -u lavad -f --no-hostname -o cat

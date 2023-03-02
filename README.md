# Lava-Snapshots (Daily update)

    sudo systemctl stop lavad

    cp $HOME/.lava/data/priv_validator_state.json $HOME/.lava/priv_validator_state.json.backup 

    lavad tendermint unsafe-reset-all --home $HOME/.lava --keep-addr-book 
    curl https://snapshot.lava.aknodes.net/snapshot-lava.AKNodes.lz4 | lz4 -dc - | tar -xf - -C $HOME/.lava

    mv $HOME/.lava/priv_validator_state.json.backup $HOME/.lava/data/priv_validator_state.json 

    sudo systemctl start lavad
    sudo journalctl -u lavad -f --no-hostname -o cat

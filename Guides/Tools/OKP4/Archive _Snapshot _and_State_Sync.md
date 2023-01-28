# Archive_Snapshot OKP4 --chain-id okp4-nemeton-1
## Snapshots are updated on daily basis. It can be used to sync archive node.
### Latest snapshot - height 661492; size ≈ 200GB; pruning: nothing; indexer: kv
### Link to snapshot http://65.108.142.47:8000/
### Archive RPC http://65.108.142.47:26617

```bash
# install lz4
apt update
apt install snapd -y
snap install lz4

# because of the snapshot size it is a good idea to unpack it using tmux or screen
apt install tmux -y
tmux new-session -s download
# to leave session CTRL+B D
# to connect again - tmux attach -t download

systemctl stop okp4d

# copy validator state to prevent accidential double signing
cp $HOME/.okp4d/data/priv_validator_state.json $HOME/.okp4d/priv_validator_state.json.backup

okp4d tendermint unsafe-reset-all --home $HOME/.okp4d --keep-addr-book

# download wasm if necessary
curl -L http://65.108.142.47:8000/wasm.tar.lz4 | lz4 -dc - | tar -xf - -C $HOME/.okp4d --strip-components 2

# download snapshot (data folder)
curl -L http://65.108.142.47:8000/data.tar.lz4 | lz4 -dc - | tar -xf - -C $HOME/.okp4d --strip-components 2

mv $HOME/.okp4d/priv_validator_state.json.backup $HOME/.okp4d/data/priv_validator_state.json

systemctl restart okp4d && journalctl -u okp4d -f -o cat
```

# State Sync OKP4 --chain-id okp4-nemeton-1
## This is Archive RPC (with all blockchain history) - can be used to statesync or for Explorers and Dashboards.

```bash
# stop node
sudo systemctl stop okp4d
okp4dd tendermint unsafe-reset-all --home $HOME/.okp4d --keep-addr-book
rm -Rf $HOME/.okp4d/wasm

SEEDS=""
PEERS="99A4C030C454DEA4BEA7A2B635886C1887DEDE6A9FA1DCDA605C4E687AE27F25@65.108.142.47:26616"
sed -i.bak -e "s/^seeds *=.*/seeds = \"$SEEDS\"/; s/^persistent_peers *=.*/persistent_peers = \"$PEERS\"/" $HOME/.okp4d/config/config.toml

SNAP_RPC="http://65.108.142.47:26617"

LATEST_HEIGHT=$(curl -s $SNAP_RPC/block | jq -r .result.block.header.height); \
BLOCK_HEIGHT=$((LATEST_HEIGHT)); \
TRUST_HASH=$(curl -s "$SNAP_RPC/block?height=$BLOCK_HEIGHT" | jq -r .result.block_id.hash)

echo $LATEST_HEIGHT $BLOCK_HEIGHT $TRUST_HASH

sed -i.bak -E "s|^(enable[[:space:]]+=[[:space:]]+).*$|\1true| ; \
s|^(rpc_servers[[:space:]]+=[[:space:]]+).*$|\1\"$SNAP_RPC,$SNAP_RPC\"| ; \
s|^(trust_height[[:space:]]+=[[:space:]]+).*$|\1$BLOCK_HEIGHT| ; \
s|^(trust_hash[[:space:]]+=[[:space:]]+).*$|\1\"$TRUST_HASH\"| ; \
s|^(seeds[[:space:]]+=[[:space:]]+).*$|\1\"\"|" $HOME/.okp4d/config/config.toml

# start node
sudo systemctl restart okp4d && journalctl -u okp4d -f -o cat

# disable State Sync after node synchronization
sed -i.bak -E "s|^(enable[[:space:]]+=[[:space:]]+).*$|\1false|" $HOME/.okp4d/config/config.toml
```

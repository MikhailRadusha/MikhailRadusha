# State Sync OKP4 --chain-id okp4-nemeton-1

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

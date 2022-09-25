
# Set up ping pub for your cosmos chains

## 1. Update packages
```
sudo apt update && sudo apt upgrade -y
```

## 2. Install nginx
```
sudo apt install nginx -y
```

## 3. Install nodejs
```
curl -fsSL https://deb.nodesource.com/setup_16.x | sudo -E bash -
sudo apt-get install -y nodejs
```

### 4. Install yarn
```
curl -sL https://dl.yarnpkg.com/debian/pubkey.gpg | gpg --dearmor | sudo tee /usr/share/keyrings/yarnkey.gpg >/dev/null
echo "deb [signed-by=/usr/share/keyrings/yarnkey.gpg] https://dl.yarnpkg.com/debian stable main" | sudo tee /etc/apt/sources.list.d/yarn.list
sudo apt-get update && sudo apt-get install yarn
```

## 3. Install binaries
```
cd ~
git clone https://github.com/ping-pub/explorer.git
cd explorer
```

## 4. Cleanup predefined chains
```
rm $HOME/explorer/src/chains/mainnet/*
```

## 5. Add testnet chains
```

```

## 6. Build ping.pub
```
yarn && yarn build
cp -r $HOME/explorer/dist/* /var/www/html
sudo systemctl restart nginx
```

## Fullnode
steps for run a full node on the binance smart chain.

##### 1.  Download the pre-build binaries from  [release page](https://github.com/bnb-chain/bsc/releases/latest)  or follow the instructions below:

###### Linux
```
wget   $(curl -s https://api.github.com/repos/bnb-chain/bsc/releases/latest |grep browser_ |grep geth_linux |cut -d\" -f4)
```

###### MacOS
```
wget   $(curl -s https://api.github.com/repos/bnb-chain/bsc/releases/latest |grep browser_ |grep geth_mac |cut -d\" -f4)
```

##### 2.  Download the config files
```
wget  $(curl -s https://api.github.com/repos/bnb-chain/bsc/releases/latest |grep browser_ |grep mainnet |cut -d\" -f4)  
```
```
mkdir ~/bsc_mainnet_node
mv mainnet.zip ~/bsc_mainnet_node
mv geth_linux geth
chmod +x geth
cd ~/bsc_mainnet_node
sudo apt install unzip
unzip mainnet.zip
cd ../
./geth --datadir ~/bsc_mainnet_node/node init bsc_mainnet_node/genesis.json
```

##### 3.  Download snapshot
Go to the  [snapshots repo](https://github.com/bnb-chain/bsc-snapshots), inside this repo you will find three links for three different snapshots, copy the links and paste them in the following command

```
sudo apt install aria2
aria2c -o geth.tar.lz4 -x 4 -s 12 "URL TO ASIA ENDPOINT" "URL TO EU ENDPOINT" "URL TO US ENDPOINT"
```

>At the moment of writing 14/07/2022 the following command contains the correct links
```
sudo apt install aria2
aria2c -o geth.tar.lz4 -x 4 -s 12 "https://tf-dex-prod-public-snapshot-site1.s3-accelerate.amazonaws.com/geth-20220713.tar.lz4?AWSAccessKeyId=AKIAYINE6SBQPUZDDRRO&Signature=FR6PAcAQjWv28sfuUlWQPgp69O8%3D&Expires=1660370182" "https://tf-dex-prod-public-snapshot.s3-accelerate.amazonaws.com/geth-20220713.tar.lz4?AWSAccessKeyId=AKIAYINE6SBQPUZDDRRO&Signature=fPl3%2BQndZNePzn8fOUBGPAjL4jI%3D&Expires=1660370183" "https://tf-dex-prod-public-snapshot-site3.s3-accelerate.amazonaws.com/geth-20220713.tar.lz4?AWSAccessKeyId=AKIAYINE6SBQPUZDDRRO&Signature=D%2FEJp9M5FCH4DJGgaqIK2pLUPNY%3D&Expires=1660370183"
```
##### 4.  Extract snapshot
```
sudo apt install pv
pv geth.tar.lz4 | tar -I lz4 -x
```

> If the following error not recognize lz4 try installing liblz4-tool with the following command
`sudo apt install liblz4-tool`

##### 5.  Replace Data
-   First, stop the running bsc client if you have one by  `kill {pid}`, and make sure the client has shut down.
-   Consider backing up the original data:  `mv ~/bsc_mainnet_node/node/geth/chaindata ~/bsc_mainnet_node/node/geth/chaindata_backup; mv ~/bsc_mainnet_node/node/geth/triecache ~/bsc_mainnet_node/node/geth/triecache_backup`

-   Replace the data:  
```
rm -rf ~/bsc_mainnet_node/node/geth/chaindata
rm -rf ~/bsc_mainnet_node/node/geth/triecache
mv server/data-seed/geth/chaindata ~/bsc_mainnet_node/geth/node/chaindata
mv server/data-seed/geth/triecache ~/bsc_mainnet_node/geth/node/triecache
```
-   Start the bsc client again and check the logs


##### 6.  Run the node

```
/home/<your_username>/geth --config /home/<your_username>/bsc_mainnet_node/config.toml --datadir /home/<your_username>/bsc_mainnet_node/node --cache 100000 --rpc.allow-unprotected-txs --txlookuplimit 0  --maxpeers 100   --rpc --syncmode=full --snapshot=false --diffsync --rpcvhosts="localhost" --ws --http
```

### Optional Steps  

**Add tmux**

*   install tmux
*   set tmux shell

```plaintext
nano ~/.tmux.conf
```

*   add these lines to the file

```plaintext
set -g default-shell "/bin/bash"
set -g default-command "/bin/bash"
```

*   restart tmux:

```plaintext
tmux kill-server; tmux
```

**Set GETH as service**

*   create a bash script:

```plaintext
nano startgeth.sh
```

*   paste this line in the script and save

```plaintext
/home/selfnode/geth --config /home/selfnode/bsc_mainnet_node/config.toml --datadir /home/selfnode/node --cache 100000 --rpc.allow-unprotected-txs --txlookuplimit 0 --maxpeers 100 --rpc --syncmode=full --snapshot=false --diffsync --rpcvhosts="localhost" --ws --http 2>> geth.log
```

*   next create the service file

```plaintext
sudo nano /lib/systemd/system/geth.service
```

* Enter the following code and save

```plaintext
[Unit]
Description=Ethereum go client
[Service]
User=
Type=simple
WorkingDirectory=/home/
ExecStart=/bin/bash /home//startgeth.sh
Restart=on-failure
RestartSec=5
[Install]
WantedBy=default.target
```

* enable the service

```plaintext
sudo systemctl enable geth
```

* start it (make sure geth isn't already running!):

```plaintext
sudo systemctl start geth
```

To check the logs you can use: `tail -F geth.log`

If necessary you can stop or restart the service with `sudo systemctl stop geth` and `sudo systemctl restart geth`

## References

- https://docs.bnbchain.org/docs/validator/fullnode/
- https://github.com/bnb-chain/bsc-snapshots
- https://github.com/bnb-chain/bsc/issues/545#issuecomment-971661245
- https://medium.com/@pieterc/running-a-full-node-with-geth-bsc-610085141248

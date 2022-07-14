## Fullnode
steps for run a full node on the binance smart chain.

##### 1.  Download the pre-build binaries from  [release page](https://github.com/bnb-chain/bsc/releases/latest)  or follow the instructions below:
```
# Linux
wget   $(curl -s https://api.github.com/repos/bnb-chain/bsc/releases/latest |grep browser_ |grep geth_linux |cut -d\" -f4)
```
```
# MacOS
wget   $(curl -s https://api.github.com/repos/bnb-chain/bsc/releases/latest |grep browser_ |grep geth_mac |cut -d\" -f4)
```

##### 2.  Download the config files
```
wget  $(curl -s https://api.github.com/repos/bnb-chain/bsc/releases/latest |grep browser_ |grep mainnet |cut -d\" -f4)  
```
```
sudo mkdir ~/bsc_mainnet_node
mv mainnet.zip ~/bsc_mainnet_node
cd ~/bsc_mainnet_node
unzip mainnet.zip
mv geth_linux geth
chmod +x geth
./geth --datadir node init genesis.json
```

##### 3.  Download snapshot
Go to the  [snapshots repo](https://github.com/bnb-chain/bsc-snapshots), inside this repo you will find three links for three different snapshots, copy the links and paste them in the following command

```
aria2c -o geth.tar.lz4 -x 4 -s 12 "URL TO ASIA ENDPOINT" "URL TO EU ENDPOINT" "URL TO US ENDPOINT"
```

>At the moment of writing 14/07/2022 the following command contains the correct links
```
aria2c -o geth.tar.lz4 -x 4 -s 12 "https://tf-dex-prod-public-snapshot-site1.s3-accelerate.amazonaws.com/geth-20220713.tar.lz4?AWSAccessKeyId=AKIAYINE6SBQPUZDDRRO&Signature=FR6PAcAQjWv28sfuUlWQPgp69O8%3D&Expires=1660370182" "https://tf-dex-prod-public-snapshot.s3-accelerate.amazonaws.com/geth-20220713.tar.lz4?AWSAccessKeyId=AKIAYINE6SBQPUZDDRRO&Signature=fPl3%2BQndZNePzn8fOUBGPAjL4jI%3D&Expires=1660370183" "https://tf-dex-prod-public-snapshot-site3.s3-accelerate.amazonaws.com/geth-20220713.tar.lz4?AWSAccessKeyId=AKIAYINE6SBQPUZDDRRO&Signature=D%2FEJp9M5FCH4DJGgaqIK2pLUPNY%3D&Expires=1660370183"
```
##### 4.  Extract snapshot
```
tar -I lz4 xvf geth.tar.lz4
```

> If the following error not recognize lz4 try installing liblz4-tool with the following command
`sudo apt install liblz4-tool`

##### 5.  Replace Data
-   First, stop the running bsc client if you have one by  `kill {pid}`, and make sure the client has shut down.
-   Consider backing up the original data:  `mv ~/bsc_mainnet_node/geth/chaindata ~/bsc_mainnet_node/geth/chaindata_backup; mv ~/bsc_mainnet_node/geth/triecache ${BSC_DataDir}/geth/triecache_backup`
-   Replace the data:  `mv server/data-seed/geth/chaindata ~/bsc_mainnet_node/geth/chaindata; mv server/data-seed/geth/triecache ~/bsc_mainnet_node/geth/triecache`
-   Start the bsc client again and check the logs


## References

https://docs.bnbchain.org/docs/validator/fullnode/
https://github.com/bnb-chain/bsc-snapshots
https://github.com/bnb-chain/bsc/issues/545#issuecomment-971661245
https://medium.com/@pieterc/running-a-full-node-with-geth-bsc-610085141248

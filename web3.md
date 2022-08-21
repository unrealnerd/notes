# web3 notes

### notes

- [adding BSC to MetaMask](https://academy.binance.com/en/articles/connecting-metamask-to-binance-smart-chain)
- [connecting metamask to bsc](https://dapp-world.com/blogs/01/how-to-connect-metamask-to-binance-smart-chainbsc--1618137142422)
- [BSC scan](https://testnet.bscscan.com/address/0xa8dC4fB8DB32A09499cA53dD75F7B6a74F49c05e#events)
- [BNB test network add token](https://testnet.binance.org/faucet-smart)
- [Wazirx NFT web ui](http://wazirxnftdevfrontend-env.eba-k5ubiwwu.ap-southeast-1.elasticbeanstalk.com/nft/50)
- [go client for ethereum using docker](https://geth.ethereum.org/docs/install-and-build/installing-geth#install-on-windows)
- [standards doc](https://docs.openzeppelin.com/contracts/2.x/tokens#ERC721)
- [to generate contract.sol](https://wizard.openzeppelin.com/)
- to compile solidity contract
```bash
    docker run -v ${pwd}:/sources ethereum/solc:stable -o /sources/contracts/output --abi --bin --overwrite /sources/contracts/WazirXNFT.sol --allow-paths /sources/node_modules
    
    docker run -v ${pwd}:/sources -it ethereum/client-go:alltools-stable
    
    abigen --abi=/sources/contracts/output/WazirXNFT.abi --pkg=connectors --out=/sources/contracts/output/WazirXNFT.go --bin /sources/contracts/output/WazirXNFT.bin
    
    go get
```


    https://testnet.bscscan.com/address/0x6C2B386d71d6Da1ec65375601986EfaF5Ad0D2e2#events
    https://remix.ethereum.org/
    http://wazirxnftdevfrontend-env.eba-k5ubiwwu.ap-southeast-1.elasticbeanstalk.com/

    setting up wsl
    ```
    sudo apt install golang-go
    sudo apt install gccgo-go
    ```
    `.\geth.exe --ropsten --syncmode "light"` 
    Public address of the key:   0x392F0E188e9D238cC2c3Ca8299eb431a5e3E1a43
    Path of the secret key file: C:\Users\arjun_shetty\AppData\Local\Ethereum\keystore\UTC--2021-05-06T14-38-58.853541300Z--392f0e188e9d238cc2c3ca8299eb431a5e3e1a43

### DeFi

- Liquidity pool token 1 and token 2 makes up the total value of the pool *x * y = k*
- *Order book* exchange is like peer to peer need another party on the other end to be willing to exchange
- *Automated Market Maker* to exchange tokens we do need another peer to be ready instead its directly from traded from the liquidity pool.
- `HODL` mispell by an investor - means hold irrescpective of the market emotions
- `BUIDL` coined from HODL means build on top of the blockchain instead of idling
- flash loans - repayment should be done on the same tracaction. in the second part of the trnsaction you take advantage of arbitarage in DeX's.
- UNI swap helpful resources
    - [whitepaper](https://hackmd.io/C-DvwDSfSxuh-Gd4WKE_ig)
    - [Is liquidity good deal](https://pintail.medium.com/uniswap-a-good-deal-for-liquidity-providers-104c0b6816f2)
- 10 k project image generator [Hashlips](https://github.com/HashLips)
